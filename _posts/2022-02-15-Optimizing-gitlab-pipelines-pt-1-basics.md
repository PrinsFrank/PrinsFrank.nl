---
title: Optimizing Gitlab pipelines - Basics (1)
subtitle: Dependency proxy, Stages, Interruptible jobs, Only running necessary jobs and asset caching
Description:
categories: [development]
tags: []
readtime:
---

The last few months I've been working a lot on optimizing our Gitlab pipelines for a large private codebase to keep the runtime of them below 4 minutes. As there is not a lot of info about optimizing Gitlab pipelines for PHP projects in general, I decided it was time to change that. As there is a lot to unpack here, this will be the first in a multipart series.

In this first part I will mostly focus on general optimizations for pipeline speed.

---

## Dependency proxy for image

When you use specific docker image, make sure you have the [Dependency Proxy](https://docs.gitlab.com/ee/user/packages/dependency_proxy/) enabled so the image doesn't have to be downloaded again for every job. Use the proxy when you specify the image in your '.gitlab-ci.pml' using the CI_DEPENDENCY_PROXY_* variables;

```yml
image: ${CI_DEPENDENCY_PROXY_GROUP_IMAGE_PREFIX}/alpine:latest
```

---

## Stages

To make sure that jobs that can run in parallel actually do, stages are used to group items that can run at the same time. They need to be defined at two locations; with the global keyword 'stages' all available stages can be defined:

```yml
stages:
  - build
  - test 
```

And every job needs to be part of a stage:
```yml
job-name:
  stage: build
```

---

## Interruptible jobs

Sometimes, you push new commit(s) before the current pipeline is finished. Instead of waiting for all jobs to finish, [you can mark jobs as interruptible](https://docs.gitlab.com/ee/ci/yaml/#interruptible) which signals a job to cancel when a new pipeline starts for the same branch;

```yml
job-name:
  stage: stage
  interruptible: true
```

As a general guideline, I try to mark all jobs as interruptible as it doesn't make sense to wait for builds and tests based on old information. Deployment jobs are the main exception as they should probably finish.

---

## Only running necessary jobs

In certain cases it doesn't add any value to execute a job. For example, when a merge request is opened that hasn't changed anything in the composer.lock or composer.json files, why would we run another composer validate command? We can speed up our merge request pipeline by only running it when specific files have changed;

```yml
composer-validate:
  stage: test
  interruptible: true
  script:
    - composer validate --strict
  rules:
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
      changes:
        - 'composer.json'
        - 'composer.lock'
    - if: '$CI_COMMIT_BRANCH == "main"'
  needs:
    - job: build-composer
  cache:
    - key:
        files:
          - composer.lock
      paths:
        - vendor/
      policy: pull
```

This way, the job will always run on main to make sure our main branch always passes, but is only run on merges when either of the files have been changed.

Another example; We have php-cs-fixer and want to make sure our code style is consistent. In this case our change list is a bit different, as besides changes to php files our 'php-cs-fixer.dist.php', 'gitlab-ci.yml' and 'composer.lock' also can affect if our style check passes;

```yml
php-cs:
  stage: test
  interruptible: true
  script:
    - php vendor/bin/php-cs-fixer fix --dry-run
  rules:
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
      changes:
        - '.gitlab-ci.yml'
        - '.php-cs-fixer.dist.php'
        - 'composer.lock'
        - '**/*.php'
    - if: '$CI_COMMIT_BRANCH == "main"'
  needs:
    - job: build-composer
  cache:
    - key:
        files:
          - composer.lock
      paths:
        - vendor/
      policy: pull
```

As a general guideline I try to always run all jobs on the main branch, to prevent random branches failing when somehow some code gets into the main branch without triggering a failing job on a merge request.

---

To prevent the 'vendor' and 'node_modules' folder from being regenerated in every job, we can configure a build job for composer and npm assets.

## Building Composer assets

To fill the vendor folder with the packages specified in the 'composer.lock' (or 'composer.json' if you don't have a lock file and are working on a package), we can add a job that runs 'composer install';

```yml
build-composer:
  stage: build
  interruptible: true
  script:
    - composer i
```

To share assets between multiple stages, [Gitlab has caches and artifacts. For dependencies we should use caches](https://docs.gitlab.com/ee/ci/caching/#how-cache-is-different-from-artifacts).

There are multiple ways we can specify when our cache is valid. For the composer vendor dir we can use the 'composer.lock' file as it is always the same for the same vendor folder;

```yml
build-composer:
  stage: build
  interruptible: true
  script:
    - composer i
  cache:
    - key:
        files:
          - composer.lock
      paths:
        - vendor/
      policy: pull-push
```

The pull-push policy is the default, but specified here for clarity. What happens here the first time a new 'composer.lock' file is detected, is that there doesn't exist a cache yet for the vendor folder with the current 'composer.lock' file, so no cache is pulled. Composer install is executed, and the vendor folder that is the result of this is pushed to the directory cache for vendor/ with this specific 'composer.lock' file. All consecutive runs for the build step with the same 'composer.lock' file don't update the cache. 

On all depending pipelines in next stages, we can specify what cache is needed by the specific job. For example;

```yml
composer-validate:
  stage: test
  interruptible: true
  script:
    - composer validate --strict
  needs:
    - job: build-composer
  cache:
    - key:
        files:
          - composer.lock
      paths:
        - vendor/
      policy: pull
```

Here we specifically set the policy to 'pull' as there are no items in the vendor folder updated, and we don't want to spend time on updating our cache.

### Fallback vendor cache

When any package in the 'composer.lock' is added, updated or deleted, the entire vendor folder is regenerated, even if only a small portion of the packages are changed. We can optimize this and configure a fallback cache, that is periodically update with a 'base' vendor folder. Only the changed packages then need to be changed and pushed to the new cache.

To seperate the logic, a new stage can be added to the global configuration:

```yml
stages:
  - build
  - test
  - cache
```

And a new job can be added to update the global cache based on the cache for the latest 'composer.lock' file on main;

```yml
update-composer-fallback-cache:
  stage: cache
  interruptible: true
  script:
    - echo 'Updating the Composer fallback cache that is used for new composer.lock files'
  only:
    - main
  needs:
    - job: build-composer
  cache:
    - key:
        files:
          - composer.lock
      paths:
        - vendor/
      policy: push
    - key: composer-fallback
      paths:
        - vendor/
      policy: push
```

As the 'composer.lock' file probably doesn't change much, this job is only executed on pushes to main. This way, the cache is updated regularly, but not on every commit or merge request. The cache is pulled from the current cache for the vendor folder and a specific version of the 'composer.lock' file, and pushed to the global fallback cache. Now all we need to do is configure the scenario where a new lock file is detected. We can do this using the 'CACHE_FALLBACK_KEY' in the build-composer stage:

```yml
build-composer:
  stage: build
  interruptible: true
  script:
    - composer i
  cache:
    - key:
        files:
          - composer.lock
      paths:
        - vendor/
      policy: pull-push
  variables:
    CACHE_FALLBACK_KEY: composer-fallback-1
```

Now when a cache doesn't exist for a specific version of the 'composer.lock' file the fallback cache will be pulled and be used as a base to install/update/remove the remaining different packages.

### Composer package cache

We can take this caching even one step further: when dependencies are updated/downgraded/added/removed in multiple branches or there is a complex branch structure with different dependency versions, the packages might be downloaded by composer multiple times.

Normally, composer prevents this by caching packages in a global package cache, but in our current setup that global cache is emptied between runs as we don't save any of it. Composer does supply us [with an option to specify in what folder we want to save that cache and where to read it from](https://getcomposer.org/doc/03-cli.md#composer-cache-dir). We can pass it along to our 'composer install' command, and make sure that cache is pushed to and pulled from:

```yml
build-composer:
  stage: build
  interruptible: true
  script:
    - COMPOSER_CACHE_DIR=composer-cache composer i
  cache:
    - key: "composer-package-cache"
      paths:
        - composer-cache/
      policy: pull-push
    - key:
        files:
          - composer.lock
      paths:
        - vendor/
      policy: pull-push
  variables:
    CACHE_FALLBACK_KEY: composer-fallback-1
```

A string key is used here as we want to have one global cache, as otherwise our cache doesn't make any sense. Our complete picture now looks as follows:

{% include puml.html graph_name="gitlab-composer-cache" alt="Gitlab composer caches" aspect_ratio=141.17  %}

---

## Building NPM assets

As the process is almost the same as above for npm assets, I wont go into much detail. I will provide an example however, here you can see the build job for npm assets:

```yml
build-npm:
  stage: build
  interruptible: true
  script:
    - npm i
  cache:
    - key:
        files:
          - package-lock.json
      paths:
        - node_modules/
      policy: pull-push
  variables:
    CACHE_FALLBACK_KEY: npm-fallback-1
```

And the job to update the global cache;

```yml
update-npm-fallback-cache:
  stage: cache
  interruptible: true
  script:
    - echo 'Updating the NPM fallback cache that is used for new package-lock.json files'
  only:
    - main
  needs:
    - job: build-npm
  cache:
    - key:
        files:
          - package-lock.json
      paths:
        - node_modules/
      policy: pull
    - key: npm-fallback
      paths:
        - node_modules/
      policy: push
```

---

## Multiple runners and shared caches

When you have multiple runners across several hosts that pick up your jobs, they will run on different hosts and probably in their own docker container. In that case, the cache feature doesn't work by default until you set up a distributed runner cache. I won't go into detail about it as the [gitlab docs has an extensive section about it on their documentation page](https://docs.gitlab.com/runner/configuration/autoscale.html#distributed-runners-caching).

This concludes the first part of this series. Sit tight for the next part!
