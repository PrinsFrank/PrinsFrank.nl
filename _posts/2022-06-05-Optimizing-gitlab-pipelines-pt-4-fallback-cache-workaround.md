---
title: Optimizing Gitlab pipelines - Fallback caches workaround (4)
subtitle: Workaround for gitlab fallback caches not updating
Description:
categories: [development]
tags: []
readtime: 3
---

The last few months I've been working a lot on optimizing our Gitlab pipelines for a large private codebase to keep the runtime of them below 4 minutes. As there is not a lot of info about optimizing Gitlab pipelines for PHP projects in general, I decided it was time to change that. As there is a lot to unpack here, this will be the fourth in a multipart series and also the final one for now as I'm switching jobs. If you haven't read the previous posts in this series, you can do it here: [part 1 about basics](/2022/02/15/Optimizing-gitlab-pipelines-pt-1-basics), [part 2 about phpunit](/2022/02/16/Optimizing-gitlab-pipelines-pt-2-phpunit) or [part 3 about phpstan](/2022/03/08/Optimizing-gitlab-pipelines-pt-3-phpstan).

In this fourth part I will focus on working around fallback caches that get outdated due to a bug in gitlab runners.

After I wrote my previous few posts, we ran into an issue: Due to some issues with our [MinIO shared cache for gitlab](https://docs.gitlab.com/charts/advanced/external-object-storage/minio.html){:target="_blank" rel="noreferrer noopener"} some cache-file references got corrupted, so we had to manually clear the cache. As it turns out, only the references to those cache files change, by incrementing an index used for caches. So when a cache was previously referenced by 'cache', it now is referenced by 'cache-1'.

This works fine for normal caches, but doesn't work with fallback caches. As my [bug report on gitlab is still open 2 months later](https://gitlab.com/gitlab-org/gitlab/-/issues/354975){:target="_blank" rel="noreferrer noopener"} and the related issues also don't seem to get solved soon, I decided to share our workaround here.

For a while, we used hardcoded indices as the caches never really get cleared. So instead of 'phpstan-fallback' we used 'phpstan-fallback-1', and whenever that cache gets cleared we simply incremented the index. But as I'm switching jobs and I'm trying to document everything as well as possible the cache index got outdated again. The last week of my job seems like a good time to figure out a more robust process for this, and a nice opportunity to write another post.

Sadly, it is not really possible to do maths with variables, so I created two variables in "Settings" => "CI/CD" => "Variables". One called "CACHE_KEY" and one called "CACHE_KEY_NEXT". The CACHE_KEY variable contains the current key index, which is 4 for us. The CACHE_KEY_NEXT is simply one integer higher, 5.

We can now use the CACHE_KEY variable in our pipelines:
```yaml
variables:
  CACHE_FALLBACK_KEY: phpstan-fallback-$CACHE_KEY
```

On every pipeline, we can also start a job that checks if the next cache exists using the CACHE_KEY_NEXT. If so, it turns the pipeline from green to orange;

```yml
check-fallback-phpstan-cache:
  stage: build
  interruptible: true
  allow_failure: true
  script:
    - if [ -d "phpstan-result-cache/" ]; then echo "New fallback phpstan cache available, increment CACHE_KEY and CACHE_KEY_NEXT in gitlab settings => 'CI/CD' => 'Variables' and run master pipeline" && exit 1; fi
  only:
    - master
    - merge_requests
    - tags
  cache:
    - key: $CI_COMMIT_SHA
      paths:
        - phpstan-result-cache
      policy: pull
  variables: # Workaround because the cache key increment bug (https://gitlab.com/gitlab-org/gitlab/-/issues/354975) is only present in the fallback code
    CACHE_FALLBACK_KEY: phpstan-fallback-$CACHE_KEY_NEXT
```

Apart from the interruptible set to 'true' so we can quickly abort this pipeline on a new commit to the branch and the allow_failure set to 'true' to only give a warning and not fail the entire pipeline, there is some trickery going on here.

Mainly, the bug is only present in the fallback cache key. So if we simply tried to get the cache for the key in the 'cache' section, the key appended would always be the latest, so we can't really check if it incremented. Instead, we use the CI_COMMIT_HASH variable to force the fallback cache to always be used, as the CI_COMMIT_HASH won't have a cache. As it won't be able to pull the cache, now it will use our fallback cache key which has the bug!

Secondly, we can't really check if a cache is pulled or not, because gitlab doesn't guarantee that a cache is always available. Instead, we can look at the side effects for a cache. In the cache of the phpstan cache from the previous post, that side effect is a 'phpstan-result-cache' folder.

So when we check for the presence of this folder, we can fail the pipeline with 'exit 1' and an appropriate message to manually increment the indices. As the allow_failure option is set, this 'failing' is subsequently turned in a warning, so the issue can be resolved without interrupting anything.

This concludes the fourth part of this series. Sit tight for the next part!
