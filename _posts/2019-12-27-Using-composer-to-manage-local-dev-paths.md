---
title: Using composer to manage local dev paths
subtitle: And how to keep them out of your composer.json and lock file so your teammates won't be angry at you
Description:
categories: [development]
tags: []
readtime: 
---

**When developing in a team where other devs have different local setups you might run into issues where you can't set the ```path``` directly in the composer.json because those paths differ between the different environments. Apparently, those values can be kept outside the composer.json by setting them globally!**

First, we need to know where the global config lives. To get the current Composer home directory, run

```shell
composer config --list --global | grep "\[home\]"
```

When you navigate to this path you, a ```config.json``` and ```auth.json``` can be found here. In my case the path is ```/home/prinsfrank/.composer```. When we open the ```config.json``` file, the initial content is fairly empty (if it doesn't exist, you can create it):

```json
{
    "config": {}
}
```

According to [the official docs](https://getcomposer.org/doc/03-cli.md#composer-home-config-json), "Composer will merge this configuration with your project's ```composer.json``` when you run the ```install``` and ```update``` commands." And both [repository](https://getcomposer.org/doc/05-repositories.md)- and [configuration](https://getcomposer.org/doc/06-config.md)settings can be set here globally. Awesome!

Now we can define the repositories. You can use any of the available options ([Composer](https://getcomposer.org/doc/05-repositories.md#composer), [VCS](https://getcomposer.org/doc/05-repositories.md#vcs), [custom package](https://getcomposer.org/doc/05-repositories.md#package-2), [PEAR](https://getcomposer.org/doc/05-repositories.md#pear), [Private packagist](https://getcomposer.org/doc/05-repositories.md#private-packagist), [Satis](https://getcomposer.org/doc/05-repositories.md#satis) or [artifact](https://getcomposer.org/doc/05-repositories.md#artifact), but we're particularly interested in the "[Path](https://getcomposer.org/doc/05-repositories.md#path)" option. 

With the path option, we can point our composer to our local working directory for specific packages and force that they are sym-linked. You can change the config.json to the following: 

```json
{
    "config": {},
    "repositories": [
         {
             "type": "path",
             "url": "/var/www/devroot/package",
             "options": {
                 "symlink": true
             }
         }
     ]
}
```

To generate the symlink successfully, we first need to add the dev version constraint to the composer.json of the package you want to use the local repository in. Let's say you have a dependency constraint in your ```require``` or ```require-dev``` section as follows:

```json
{
  "require": {
    "author/package": "^1.0.0"
  }
}
```

To also allow local packages when they are available, this needs to be changed to:

```json
{
  "require": {
    "author/package": "dev-master|^1.0.0"
  }
}
```

This points to the master branch in the local path we just defined, or if it's not set like on a test or production environment it falls back to the original version constraint.
**You need to switch to this branch in git locally while running the commands below in the package folder to be able to mount the package correctly**, so in this case the ```master``` branch. Navigate to the package directory and run:

```shell
git checkout master
```

Now navigate back to the main project and run:

```shell
composer clear-cache
```

and:

```shell
composer update
```

If everything went well you should now see something like: 

```
- Installing author/package (dev-master):
Symlinking from /var/www/devroot/package
```

One major disadvantage of this process is that the lock file will be updated and will include your local paths. obviously we don't want them in git, so we have to work around that. After the composer update we can simple revert the lock file, but we don't want to do this for every update so we can create a new command called "setup-symlinks". We also don't want to revert a file that already has changes, so we need to check if it has uncommitted changes before we can continue. Add these lines to the composer.json of the main project under the "scripts" section: 

```json
{
    "scripts": { 
        "lock:fail-on-change": "git diff --exit-code -w -s composer.lock || (echo 'Please make sure you dont have uncommitted changes to your composer lock file before you continue.' && false )",
        "lock:revert": "git checkout -- composer.lock",
        "setup-symlinks": [
            "composer lock:fail-on-change",
            "composer clear-cache",
            "composer update --prefer-source",
            "composer lock:revert"
        ]
    }
}
```

When you now run ```composer setup-symlinks``` your symlinks will be taken care off without affecting your lock file!

When you add the ```"preferred-install": "dist""``` to the composer.json, composer will not use the symlinks when updating your composer.json or lock file so you won't accidentally commit them.