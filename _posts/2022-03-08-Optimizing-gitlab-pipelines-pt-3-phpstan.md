---
title: Optimizing Gitlab pipelines - PHPStan (3)
subtitle: Caching caches
Description:
categories: [development]
tags: []
readtime:
---

The last few months I've been working a lot on optimizing our Gitlab pipelines for a large private codebase to keep the runtime of them below 4 minutes. As there is not a lot of info about optimizing Gitlab pipelines for PHP projects in general, I decided it was time to change that. As there is a lot to unpack here, this will be the third in a multipart series. If you haven't read the previous posts in this series, you can do it here: [part 1 about basics](/2022/02/15/Optimizing-gitlab-pipelines-pt-1-basics) or [part 2 about phpunit](/2022/02/16/Optimizing-gitlab-pipelines-pt-2-phpunit).

In this third part I will focus on optimizing PHPStan pipelines.

---

Besides maybe generating coverage, PHPStan is the most time-consuming job in our pipeline. Mostly because we have a lot of rule extensions like [Larastan for Laravel support](https://github.com/nunomaduro/larastan), [PHPUnit support](https://github.com/phpstan/phpstan-phpunit), [PHPStan strict rules](https://github.com/phpstan/phpstan-strict-rules) and [PHPStan deprecation rules](https://github.com/phpstan/phpstan-deprecation-rules), but also because our level is set to 9 in a legacy project, which causes a huge baseline and slows everything down even further. 

Without the optimizations I will discuss here, an entire run for only the PHPStan job takes a whopping 19 minutes, not a time you want to wait for.

---

## The PHPStan cache

PHPStan does have a caching functionality, but it is very strict in when it will be used and when it will be disregarded. The most important factor is that you always have to run a [full analysis of your project](https://phpstan.org/blog/why-you-should-always-analyse-whole-project), otherwise you will miss out on errors outside the scope related to your file changes. 

The result cache is [valid for at least 7 days, or when any of the files are changed that are automatically reason for a new full run](https://phpstan.org/user-guide/result-cache). But normally when you configure a pipeline that cache is not shared, and not used on consecutive runs on that same branch or other branches. Let's change that! Say for example you have the following base job:

```yml
phpstan:
  stage: testphp
  interruptible: true
  script:
    - php -d memory_limit=4G vendor/bin/phpstan analyze
  cache:
    - key:
        files:
          - composer.lock
      paths:
        - vendor/
      policy: pull
  needs:
    - job: build-composer
```

The job [is interruptible in case a new commit is pushed before the job is finished](/2022/02/15/Optimizing-gitlab-pipelines-pt-1-basics#interruptible-jobs), and it depends on [our previously set up composer assets](/2022/02/15/Optimizing-gitlab-pipelines-pt-1-basics#building-composer-assets). Besides that, basically the only thing here is our PHPStan command with a custom memory limit as PHPStan is quite memory greedy.

---

## The missing 'tmpDir' command line argument

To cache our cache, we need to be able to have access to it. By default, the tempfolder in gitlab is not sharable using caches and [we only can cache files inside the root directory](https://docs.gitlab.com/ee/ci/yaml/index.html#cachepaths), so we need to move it to an accessible location. There is a configuration key for it: [tmpDir](https://phpstan.org/config-reference#caching). But it can only be used in the phpstan.neon file, and there is no command line argument for it available. And we also don't want to have the cache in another folder locally. So instead of changing the tmpDir in our phpstan.neon or having a mirror phpstan.neon which is bound to cause some sync issues between the two files, we change the file right before runtime. Our simple one-liner for running phpstan now becomes the following;

```yml
  script:
    - 'awk -v n="parameters:\n    tmpDir: phpstan-result-cache" "NR==FNR&&/parameters:/{l=NR};NR!=FNR{if(FNR==l){print n;}else print}" phpstan.neon phpstan.neon > phpstan_new.neon' # Hack to only set the tmpDir in the pipeline as there is no command line argument
    - mv phpstan_new.neon phpstan.neon
    - php -d memory_limit=4G vendor/bin/phpstan analyze
```

Now, anytime this command runs, the cache is read from and written to in a folder called 'phpstan-result-cache' in the root of our project.

## Creating a branch-cache

Now we have our cache available to us, we need to write to it and read from it on consecutive runs. We can create a branch/tag specific cache for that folder;

```yml
  cache:
    - key: "$CI_COMMIT_REF_SLUG-phpstan"
      paths:
        - phpstan-result-cache
      policy: pull-push
      when: always
```

The 'when' option is set to always, because we don't want to waste any precious minutes on rebuilding the entire cache when PHPStan detected an issue and our branch failed. This option makes sure that the cache is update even on failure.

So after our first 19 minute PHPStan run on a specific branch, all consecutive runs now take somewhere between half a minute and 2 minutes.

## Global fallback cache for first runs.

Now to brush away that first 19 minute run for a branch, we need to repeat the trick for a [global fallback cache](/2022/02/15/Optimizing-gitlab-pipelines-pt-1-basics#fallback-vendor-cache). We only need to add 2 lines to our job;

```yml
  variables:
    CACHE_FALLBACK_KEY: phpstan-fallback
```

To make sure our global fallback cache for phpstan is as up-to-date as possible, we also add a new job on changes on the main branch:

```yml
update-phpstan-fallback:
  stage: cache
  interruptible: true
  script:
    - echo 'Updating the PHPStan fallback cache that is used for new branches'
  only:
    - main
  needs:
    - job: phpstan
  cache:
    - key: "$CI_COMMIT_REF_SLUG-phpstan"
      paths:
        - phpstan-result-cache
      policy: pull
    - key: phpstan-fallback
      paths:
        - phpstan-result-cache
      policy: push
```

This job needs the previous PHPStan run and its branch cache for main, and copies it to the global fallback cache. Now all first pipelines on new branches and tags also can use a cache!

---

## Only running the pipeline on related file changes

Our final task is to make sure this job runs as few times as possible, and only when we really need it to run. So only if there are files changed related to phpstan we want to run this job. We also want to always run this on main, to make sure that we always have a valid main state;

```yml
  rules:
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
      changes:
        - '.gitlab-ci.yml'
        - 'phpstan.neon'
        - 'phpstan-baseline.neon'
        - 'composer.lock'
        - 'app/**/*.php'
        - 'app/*.php'
        - 'tests/**/*.php'
        - 'tests/*.php'
        - 'config/**/*.php'
        - 'config/*.php'
        - 'development/PHPStan/**/*.php'
        - 'development/PHPStan/*.php'
    - if: '$CI_COMMIT_BRANCH == "main"'
```

As this is a laravel application, we have a folder structure with multiple php files in the app, tests and config folder. For our custom project-specific rules, we also have a development/PHPStan folder. Besides that, any change to phpstan.neon or the baseline or composer.lock file will also probably result in changes to the outcome of running PHPStan.

## All changes together

Our caching schema now looks like this:

{% include puml.html graph_name="gitlab-phpstan-cache" alt="Gitlab composer caches" aspect_ratio=170.17  %}

And our two jobs in the yaml file now look like this; (For the composer build steps you can reference the [first post in this series](/2022/02/15/Optimizing-gitlab-pipelines-pt-1-basics))

```yml
phpstan:
  stage: testphp
  interruptible: true
  script:
    - 'awk -v n="parameters:\n    tmpDir: phpstan-result-cache" "NR==FNR&&/parameters:/{l=NR};NR!=FNR{if(FNR==l){print n;}else print}" phpstan.neon phpstan.neon > phpstan_new.neon' # Hack to only set the tmpDir in the pipeline as there is no command line argument
    - mv phpstan_new.neon phpstan.neon
    - php -d memory_limit=4G vendor/bin/phpstan analyze
  cache:
    - key: "$CI_COMMIT_REF_SLUG-phpstan"
      paths:
        - phpstan-result-cache
      policy: pull-push
      when: always
    - key:
        files:
          - composer.lock
      paths:
        - vendor/
      policy: pull
  variables:
    CACHE_FALLBACK_KEY: phpstan-fallback
  rules:
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
      changes:
        - '.gitlab-ci.yml'
        - 'phpstan.neon'
        - 'phpstan-baseline.neon'
        - 'composer.lock'
        - 'app/**/*.php'
        - 'app/*.php'
        - 'tests/**/*.php'
        - 'tests/*.php'
        - 'config/**/*.php'
        - 'config/*.php'
        - 'development/PHPStan/**/*.php'
        - 'development/PHPStan/*.php'
    - if: '$CI_COMMIT_BRANCH == "main"'
  needs:
    - job: build-composer

update-phpstan-fallback:
  stage: cache
  interruptible: true
  script:
    - echo 'Updating the PHPStan fallback cache that is used for new branches'
  only:
    - main
  needs:
    - job: phpstan
  cache:
    - key: "$CI_COMMIT_REF_SLUG-phpstan"
      paths:
        - phpstan-result-cache
      policy: pull
    - key: phpstan-fallback
      paths:
        - phpstan-result-cache
      policy: push
```

This concludes the third part of this series. Sit tight for the next part!
