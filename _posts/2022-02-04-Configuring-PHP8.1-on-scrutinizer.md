---
title: Configuring PHP8.1 on Scrutinizer
subtitle: A frustrating amount of time spent on a simple fix
Description:
categories: [development]
tags: []
readtime:
---

When building open source PHP packages, Scrutinizer is an awesome free tool to analyse code and create coverage reports. As I recently started a [Validated Properties](https://github.com/PrinsFrank/php-validated-properties) package I decided to implement it there as well, but unlike with other packages this one doesn't have support for PHP7.4 and 8.0, as it uses Attributes introduced with PHP8.1. And setting up PHP8.1 on Scrutinizer turned out to quite difficult.

According to the Scrutinizer docs, using PHP8.1 should be as simple as adding a version to the php build environment variable, and: [(...) currently support the following versions: (...) <b>or any other released version</b>](https://scrutinizer-ci.com/docs/build/languages#php). But when adding setting the following configuration:

```yaml
build:
    environment:
        php: 8.1.2
```

The following error appeared:

```shell
-----------------
|  BUILD ERROR  |
-----------------

Here are the last 10 lines from the log:

-----------------------------------------
configure: error: Package requirements (openssl >= 1.0.2) were not met:

Requested 'openssl >= 1.0.2' but version of OpenSSL is 1.0.1f

Consider adjusting the PKG_CONFIG_PATH environment variable if you
installed software in a non-standard prefix.

Alternatively, you may set the environment variables OPENSSL_CFLAGS
and OPENSSL_LIBS to avoid the need to call pkg-config.
See the pkg-config man page for more details.
-----------------------------------------

The full Log is available at '/tmp/php-build.8.1.2.20220204182959.log'.
[Warn]: Aborting build.
```

My first thought was to simply update openssl. But the dependencies step only runs after the build environment step is completed, so first we need to switch to PHP8.0 to circumvent that. After adding a PPA - scrutinizer uses it's own ppa that doesn't have a newer version of openssl available - openssl was updated to 1.0.2. Now I was ready to update PHP8.0 to PHP8.1 in the dependencies step. After adding the PPA from ondrej and running the upgrade command, I was welcomed by the following error:

```shell
Reading package lists... Done
Building dependency tree       
Reading state information... Done
E: Unable to locate package php8.1
E: Couldn't find any package by regex 'php8.1'
```

Great, the Ubuntu image is [so old no packages were built for it](https://launchpad.net/~ondrej/+archive/ubuntu/php). 

After searching for scrutinizer.yaml files on github and in google containing PHP8.1 I was ready to give up. No one appeared to have struggled with this before, or at least no one reported back with an update on how to set this up. (Until now of course). All the scrutinizer.yaml files on Github that contain PHP8.1 have broken builds on Scrutinizer failing for weeks now.

As an act of desperation I decided to chat with the customer support. After typing in my question I hit send and .... got a response. Not the one I expected:

```text
You’ll get replies here and in your email:
✉️ xxxx@xxxx.com
Our usual reply time
🕒 A day
```

Great. I went to bed frustrated and forgot all about it. Until today, I got an actually useful answer, pointing me to an article on [how to switch to a different Ubuntu version](https://scrutinizer-ci.com/docs/guides/upgrading-trusty-image)!

The fix? It's easy as this:

```yaml
build:
    image: default-bionic
    environment:
        php: 8.1.2
```

That was a waste of hours. But at least I can now fully enjoy my weekend.
