---
title: Laravel Service Providers and how to speed up your application
subtitle: Deferrable providers and how they will save you those valuable first milliseconds
Description:
categories: [development]
tags: []
readtime:
---

If you have been working with Laravel you have probably touched some service providers. Laravel comes with [a bunch of them out of the box](https://github.com/laravel/laravel/blob/9.x/config/app.php#L157){:target="_blank" rel="noreferrer noopener"} to get your application starting as they are the central place to configure your application. But for a lot of developers, service providers and how they work internally remains a mystery. 

If you look in [the Laravel documentation about service providers](https://laravel.com/docs/9.x/providers){:target="_blank" rel="noreferrer noopener"}, there are two base methods to configure here, the "register" method and the "boot" method. According to the documentation "Most service providers contain a _register_ and a _boot_ method", but I would disagree with that. You'll probably only need the register method, except if you are extending framework functionality. More on that later.

But how do these service providers work, and how can we speed them up?

---

There are two entry points in a basic Laravel application; HTTP requests, which come in through nginx/apache and are typically routed to 'public/index.php', and artisan commands, which are handled through an executable artisan php file (in the root of your project). Service providers are the simplest in console commands, so let's talk about them first and expand on that.

## Console commands

When you execute an artisan by running "php artisan ..." in the root of your project, you tell php to call the artisan file with the arguments you provide. If you open that artisan file you can see what happens internally:

```php
#!/usr/bin/env php
<?php

require __DIR__.'/vendor/autoload.php';

$app = require_once __DIR__.'/bootstrap/app.php';

$kernel = $app->make(Illuminate\Contracts\Console\Kernel::class);
$status = $kernel->handle(
    $input = new Symfony\Component\Console\Input\ArgvInput,
    new Symfony\Component\Console\Output\ConsoleOutput
);

$kernel->terminate($input, $status);
exit($status);
```

What happens here, is that we load the composer autoload file, make an instance of the application and use that application to create a Kernel. We let the Kernel handle the input and output, and after that we terminate it.

In the Kernel class, the "handle" method makes sure the application is bootstrapped by calling the "bootstrap" method, which in turn calls all Bootstrapper class in the $bootstrappers property in Foundation/Console/Kernel, one of them being "RegisterProviders".

A lot of internal magic happens here, but simply said, for every service provider, the "register" method is called, and after that for every service provider the "boot" method is called. To make it more visually;

<div style="width:50%;margin: 0 25%;">
{% include puml.html graph_name="service-providers-console-simple" alt="Simple service provider registering"  aspect_ratio=139 %}
</div>

## Deferred Providers

If you've read the [Laravel documentation about Service providers](https://laravel.com/docs/9.x/providers){:target="_blank" rel="noreferrer noopener"}, You've also come across [Deferred Providers](https://laravel.com/docs/9.x/providers#deferred-providers){:target="_blank" rel="noreferrer noopener"}. These complicate the Console commands but are a necessary segue into the technical explanation about speeding up your application. Deferred Providers are loaded after all the normal service providers have been booted and registered by the "RegisterProviders" bootstrapper in the "bootstrap" method in the Console Kernel;

<div style="width:50%;margin: 0 25%;">
{% include puml.html graph_name="service-providers-console-with-deferred" alt="Service provider registering with deferred"  aspect_ratio=186 %}
</div>

So far, whether or not your ServiceProvider is deferrable doesn't really make a difference, as you can see that it gets loaded either way when booting the console. That changes when we move into HTTP request though.

## HTTP Requests

While Artisan commands are executed on a server, we can usually wait for them to finish for a couple of seconds (maybe hours or days). Requests should be lightning fast, however. Laravel has been somewhat optimized to return a response as fast as possible, one of them involving the deferrable Providers.

To dig into this deeper, we should first take a step back. While we didn't look at the bootstrap cache for console commands, it is used for commands as well. Whenever service providers are added or removed, they are cached in _"/bootstrap/cache/services.php"_ (Path may be different according to your configuration). A typical file looks like this (abbreviated for clarity);

```php
 <?php return array (
  'providers' => 
  array (
    0 => 'Illuminate\\Auth\\AuthServiceProvider',
    (...)
  ),
  'eager' => 
  array (
    0 => 'Illuminate\\Auth\\AuthServiceProvider',
    (...)
  ),
  'deferred' => 
  array (
    'foo.bar' => 'App\\Providers\\AppServiceProvider',
    (...)
  ),
  'when' => 
  array (
    'Illuminate\\Broadcasting\\BroadcastServiceProvider' => 
    array (
    ),
    (...)
  ),
);
```

We didn't need to dive into this for console commands, but this same caching mechanism is used for those. As you can see there are multiple array keys in this file. The "providers" key contains a list of _all_ service providers available in the application. But instead of registering all non-deferrable providers and then registering all deferrable providers, for HTTP requests we iterate over all keys in the "eager" section only after calling the "handle" method on the HttpKernel and sending the request through the router;

<div style="width:50%;margin: 0 25%;">
{% include puml.html graph_name="service-providers-http-eager" alt="HTTP eager service provider loading"  aspect_ratio=139 %}
</div>

And then in controllers or depending services, whenever we request a service that is not loaded and is deferrable - when calling resolve(), App::make() or something similar - we can check if the key for that service is set in the deferred section;

<div style="width:50%;margin: 0 25%;">
{% include puml.html graph_name="service-providers-http-deferred" alt="HTTP eager service provider loading"  aspect_ratio=137 %}
</div>

## Why do my providers stop working when I change their order?

As you can see in the graphs above, there is a distinction between the "register" and the "boot" method. According to the Laravel documentation, the distinction between them is as follows:

> Within the register method, you should only bind things into the service container. You should never attempt to register any event listeners, routes, or any other piece of functionality within the register method.

It doesn't explain why though. The simple answer to that is because those event listeners, routes, etc. need services that are registered in "register" methods in other service providers, and those service providers may not have their "register" method called yet. That also explains why re-ordering your service providers might 'solve' some issues with bindings.

If you want to know when to use the "register" and when to use the "boot" method, simply always use the "register" method for service container calls like "singleton", "bind", "scoped", "instance", "when", "tag", "extend", "makeWith" and "resolving", and use the boot method for everything else. Also, when you want to conditionally register other service providers, do that in the register method as well.

## So how _do_ I speed up my application?

As you may have concluded from the sections above, to speed up your HTTP requests you should make as many ServiceProviders implement the "DeferrableProvider" interface, so that the provider ends up in the "deferred" section of the "bootstrap/cache/services.php" file. One tip here: the file is not regenerated if you change anything in the provider itself, you may have to run "php artisan cache:clear" to regenerate this file. Another tip: even though you are implementing an interface, [the presence of the "provides" method is not enforced](https://github.com/laravel/framework/pull/42460){:target="_blank" rel="noreferrer noopener"} and the provider will not work without it.  

If you have any questions or troubles, please [feel free to ask by opening an issue on the Github repo for this blog](https://github.com/PrinsFrank/PrinsFrank.nl/issues/new/choose){:target="_blank" rel="noreferrer noopener"}!
