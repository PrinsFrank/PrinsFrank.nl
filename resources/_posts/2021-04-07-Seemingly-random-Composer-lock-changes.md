---
title: Seemingly random composer.lock changes
subtitle: And how to prevent them
Description:
categories: [development]
tags: []
readtime:
---

When working with multiple developers on a project, a lot can happen regarding Composer dependencies. Packages get added, removed, and upgraded. Sometimes when a package has been added, other dependencies might also need an upgrade. Or a developer accidentally upgrades all packages because they use the wrong command. (always running "composer update" is apparently a habit hard to get rid of)

Even though the composer.lock file only exist to lock the dependencies to a release of dependencies you know for sure combine to an installable release and you might not keep a close eye to it, that stable package set is an important thing to monitor. Developers make mistakes, and so do package developers. So when a package is update from x.y.1 to x.y.2, something might inadvertently break. But when every time you look at the lock file to check what is actually changed every line is highlighted, you might be inclined to just accept that pull or merge-request.

The root cause of these seemingly 'random' changes to the lock file is different developers work with different composer versions. Composer 1.0.0 is now exactly 5 years old, and a lot has changed since then. Hashing algorithms have changed, and sections have been removed and added. But not all developers are decently upgrading their packages and package managers.

I tried to generate a list of all changes between versions by running the "composer u nothing" command on every minor composer version, but was quickly reminded there is no easy way to switch between early Composer version and that the "composer self-update" command was a fairly recent addition. Instead, I generated a lock file on Composer 1.0.0 and on the most recent version of Composer - 2.0.12 - and compared the lock file. While not precisely tracking all changes and not showing what version changes what, it gives an interesting overview: 

- The url of the readme section changed between [https://getcomposer.org/doc/01-basic-usage.md#composer-lock-the-lock-file](https://getcomposer.org/doc/01-basic-usage.md#composer-lock-the-lock-file){:target="_blank" rel="noreferrer noopener"} and [https://getcomposer.org/doc/01-basic-usage.md#installing-dependencies](https://getcomposer.org/doc/01-basic-usage.md#installing-dependencies){:target="_blank" rel="noreferrer noopener"} in the lock file
- The "hash" section was renamed to "content-hash" or the other way round in the lock file.
- The "hash" or "content-hash" was recalculated even when the rest of the composer.lock hasn't changed.
- The "support" section was added or removed with individual packages in the lock file.
- The "time" section has now added or removed a "T" and timezone offset indicating localized timestamps with individual packages in the lock file.
- A "funding" section was added or removed with individual packages in the lock file.
- A "plugin-api-version" section was added or removed to the composer.lock file. 

please open a PR to add any missing changes

All these issues and more are indicators of different developers running a command that updates the lock file like "composer require vendor/package-name" or "composer update (nothing)" on different Composer versions than the previous developer.

Previously, there was no easy way to force all developers to use the same Composer version when they all have their own (virtual) machine other than to manage those machines very closely. Most developers are not that happy when they don't have full control over their OS and you update their configuration and installed packages.

Luckily there is a solution I've recently been working on. Instead of managing the installation of Composer directly, I developed a package that requires a certain version of Composer when a developer wants to run a command for that package that modifies the lock file. Besides adding a version constraint, you can also recommend a specific (bug-free/stable) version. 

To get started, run 
```shell
composer require prinsfrank/composer-version-lock
```
and
```shell
composer config extra.composer-version {VERSION_CONSTRAINT}
```
so you can require all your fellow developers working on a project to use a specific version of Composer when they want to update the lock file. Read more on the [Github repo](https://github.com/PrinsFrank/composer-version-lock){:target="_blank" rel="noreferrer noopener"}.
