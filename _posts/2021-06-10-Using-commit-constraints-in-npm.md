---
title: Using commit constraints in NPM
subtitle: And how to use that knowledge to get changes sooner
Description:
categories: [development]
tags: []
readtime: 2
---

With [1.6 million NPM packages and a 1000 new ones added every day](http://www.modulecounts.com/){:target="_blank" rel="noreferrer noopener"} - all depending on each other - it is no surprise that there will be some vulnerabilities found and [advisories published](https://www.npmjs.com/advisories){:target="_blank" rel="noreferrer noopener"}. [The worryingly high package dependency count and dependency depth](https://sambleckley.com/writing/npm.html){:target="_blank" rel="noreferrer noopener"} makes it even more likely that you will find a lot - if not eventually all - of the advisories in your audit results. One of the reasons I have a strong distaste for NPM and even javascript in general, but I will go into that further in a next post. For now, it's sadly a necessary evil.

Recently, I was upgrading our dependencies again, when I ran into several packages that still hadn't updated their dependencies several weeks after an advisory was published. While most repositories had either an open PR, or an already merged PR where the changes were not tagged in a new release, there was still a lot of confusion about how to resolve those audit results in the meantime. Referencing a commit on a fork or on the master branch before a feature is merged or a new release is tagged is a normal practice in PHP/Composer. Apparently it's a lot less known in the JS/NPM world. 

Normally, when installing a package you would do so by running:

```shell
npm install vendor/package-name
```

This will add a (dev-)dependency to your package.json, and install the latest stable version of that package. But any changes on the main branch that haven't been tagged for release won't be installed.

You could require the master/main branch instead, and get those changes:

```shell
npm install git:github.com/vendor/package-name#master
```

This is not recommended however. Any update after the change from the master/main branch you need gets installed, even the changes you don't need. And whenever a broken commit gets merged you will install that broken code on the next update - even when that broken code is not tagged for release. 

The best option in this case is to reference a commit directly. This way, we get the changes from the commit we need without the risk of further unstable changes ending up in our node_modules folder. We can reference any commit on the repository or on a fork of a repository.

Let's take the [specific scenario of Laravel Mix](https://github.com/JeffreyWay/laravel-mix/issues/2978#issuecomment-850275121){:target="_blank" rel="noreferrer noopener"}. While looking through the issues and Pull requests, I found a pull request that specifically updated the dependency. But it was not yet tagged, so not available through the normal dependencies. In the pull request, the commit hash can be found, and added as a dependency instead of the stable or branch constraints:

```shell
npm install git:github.com/vendor/package-name#b45f7a1ab2959a87e4364c6192f98a5f096ee542
```

Our dependency issues are now solved!
