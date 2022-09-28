---
title: How to write decoupled unit tests in Laravel
subtitle: And how this can make your code more testable and your tests run faster
Description:
categories: [development]
tags: []
readtime:
---

If you have been using Laravel for a while, You'll probably be familiar with the default test folder when setting up a new project: 'Feature' and 'Unit'. The distinction Laravel makes here since [two years](https://github.com/laravel/laravel/commit/f4b1dc6df04f4ef9b4b15e2c38668e8cb168c253){:target="_blank" rel="noreferrer noopener"} now is that Unit Tests extend from the base PHPUnit test case, and Feature tests extend from the ['Tests/TestCase.php' in the root of the test folder](https://github.com/laravel/laravel/blob/9.x/tests/TestCase.php){:target="_blank" rel="noreferrer noopener"}, where an application is set up and booted so you can access all the booted services. 

Typically, I'll add a Integration folder to this folder setup to test things like successful booting of service providers in the container or other integrations, but I digress.

Sometimes, how to write a Unit test is not clear. And Laravel projects that were started before the base test for Unit tests changed are probably still extending from 'Tests/TestCase.php'. And you might be missing out on some performance if this is the case.

---

## The issue with testability in Laravel

Laravel is famous for its useful static classes and global helpers. Those makes the framework very accessible for novice programmers and easy to use when prototyping a new application. And while Str::* and Arr::* methods are useful and usually harmless in a loosely coupled codebase, there are a lot of helper functions that depend on a booted framework or a 'state' to be present before they work.

We can trust that the [php8.0 function 'str_starts_with'](https://www.php.net/manual/en/function.str-starts-with.php){:target="_blank" rel="noreferrer noopener"} always returns a boolean and that the result doesn't change depending on 'external' circumstances. With 'external' in this context I mean literally anything, database, configuration, anything - except for bit flips, maybe. The same cannot be said for most laravel functions. There are only a bunch of them that are decoupled;

```php
class_basename(), e(), preg_replace_array(), str(), blank(),
class_uses_recursive(), collect(), dd(), dump(), blank(),
filled(), method_field(), now(), optional(), retry(), tap(),
throw_if(), throw_unless(), today(), trait_uses_recursive(),
transform(), value(), with();
```

Apart from these - as of Laravel 9.0 - [all global functions documented](https://laravel.com/docs/9.x/helpers){:target="_blank" rel="noreferrer noopener"}are dependent in some way or another on the state of a booted application. And while these functions are convenient ways to shorten the amount of code you have to write, they couple your code with the Laravel codebase. And furthermore, they decrease the testability of your codebase.

Writing testcases for parts of your code that changes behaviour depending on the result of these global helpers is possible, but you still need to boot your application. And because the state of your application might even change in a single test case, this is usually done in the 'setUp' method. But that also means that you are booting your application for _every single test case_, often resulting in your application being booted several hundred or -thousand times. And the more functionality - especially service providers - your application has, the slower your test execution becomes. A 5% increase in boot time of your application then also means a 5% increase in test execution time. And waiting for your tests to execute becomes cumbersome after a while.

---

## Unit Tests ❤ Dependency Injection

While you maybe haven't found a way to properly write Unit Tests or simply didn't find a reason to write Unit tests instead of Integration/Feature tests in Laravel because of the way Laravel is set up, it is possible. Instead of extending from 'Tests/TestCase.php' and calling '$this->createApplication()' a bunch of times, we can extend from the PHPUnit test case instead. But that is the easiest bit. The harder problem to solve is how to rewrite our code to actually Unit Testable code.

The good news is that all the global functions have an alternative way they can be called from a service, which can be injected by the service container. If you want to learn more about [Dependency Injection and the resolving of services by the service container, there are some excellent examples in the Laravel documentation](https://laravel.com/docs/9.x/container#resolving){:target="_blank" rel="noreferrer noopener"}. A tip here: Skip the section about the "Make" method and continue directly to the ['Automatic Injection' section](https://laravel.com/docs/9.x/container#automatic-injection){:target="_blank" rel="noreferrer noopener"} as it will make your code less coupled on the service container and your life easier when writing tests.

In short: Every class that is resolvable by service providers in the service container will be automatically bound to constructor arguments in basically any type of class that can be generated by the artisan make* commands. So if you want the get the application path, instead of calling 'app_path', add 'private \Illuminate\Contracts\Foundation\Application $application' class as a constructor argument, and call '$this->application->path()' instead.

To make your life even easier, I decided to walk through [all the documented global helpers](https://laravel.com/docs/9.x/helpers){:target="_blank" rel="noreferrer noopener"}, and give an alternative for each one. Simply ctrl+f on this page for the method you are trying to replace to make your code more testable, And there'll be an alternative below!

## Paths

Class to DI:
```php
Illuminate\Contracts\Foundation\Application $application
```

Instead of these functions, use these methods on the container service:
```diff
- app_path();
+ $application->path();
- base_path();
+ $application->basePath();
- config_path();
+ $application->configPath();
- database_path();
+ $application->databasePath();
- resource_path();
+ $application->resourcePath();
- public_path();
+ $application->publicPath();
- lang_path();
+ $application->langPath();
- storage_path();
+ $application->storagePath();
```

## Services

Class to DI:
```php
Illuminate\Contracts\Foundation\Application $application
```

Instead of these functions, use these methods on the container service:

```diff
- resolve($serviceName);
+ $application->make($serviceName);
- app();
+ $application;
- app($serviceName);
+ $application->make($serviceName);
- abort();
+ $application->abort();
- abort_if();
+ if ($boolean) {
+   $application->abort();
+ }
- abort_unless();
+ if (!$boolean) {
+   $application->abort();
+ }
```

## Translations

Class to DI:
```php
Illuminate\Contracts\Translation\Translator $translator
```

Instead of these functions, use these methods on the container service:
```diff
- __();
+ $translator->translate();
- trans();
+ $translator->translate();
- trans_choice();
+ $translator->choice();
```

## Routes

Class to DI:
```php
Illuminate\Contracts\Routing\UrlGenerator $urlGenerator
```

Instead of these functions, use these methods on the container service:
```diff
- action();
+ $urlGenerator->action();
- asset();
+ $urlGenerator->asset();
- secure_asset($path);
+ $urlGenerator->asset($path, true);
- route();
+ $urlGenerator->route();
- url();
+ $urlGenerator->url();
- url()->current();
+ $urlGenerator->current();
- url()->full();
+ $urlGenerator->full();
- url()->previous();
+ $urlGenerator->previous();
- secure_url($path, $parameters);
+ $urlGenerator->url($path, $parameters, true);
```

## Redirects

Class to DI:
```php
Illuminate\Routing\Redirector $redirector  // Redirector doesn't implement a contract
```

Instead of these functions, use these methods on the container service:

```diff
- redirect();
+ $redirector->to();
- to_route();
+ $redirector->route();
- back();
+ $redirector->back();
```

## Configuration Variables

Class to DI:
```php
Illuminate\Contracts\Config\Repository $configRepository
```

Instead of these functions, use these methods on the container service:

```diff
- config();
+ $configRepository->all();
- config($key);
+ $configRepository->get($key);
```

## Logging

Class to DI:
```php
Psr\Log\LoggerInterface $logManager
```

Instead of these functions, use these methods on the container service:
```diff
- logger();
+ $logManager;
- logger($message);
+ $logManager->debug($message);
- info($message);
+ $logManager->info($message);
```

## Exception Reporting

Class to DI:
```php
Illuminate\Contracts\Debug\ExceptionHandler $exceptionHandler
```

Instead of these functions, use these methods on the container service:
```diff
- report($message);
+ $exceptionHandler->report($message);
- rescue($callback, $rescue);
+ try {
+    return $callback();
+} catch (Throwable $e) {
+    $exceptionHandler->report($e);
+
+    return value($rescue, $e);
+}
```

## Request

Class to DI:
```php
Illuminate\Http\Request $request
```

Instead of these functions, use these methods on the container service:
```diff
- request();
+ $request
- old();
+ $request->old();
```

## Response

Class to DI:
```php
Illuminate\Contracts\Routing\ResponseFactory $responseFactory
```

Instead of these functions, use these methods on the container service:
```diff
- response();
+ $responseFactory;
- response($content);
+ $responseFactory->make($content);
```

## Mix

Class to DI:
```php
Illuminate\Foundation\Mix $mix
```

Instead of this function, use the method on the container service:
```diff
- mix();
+ $mix();
```

## Auth

Class to DI:
```php
Illuminate\Contracts\Auth\Factory $authFactory
```

Instead of these functions, use these methods on the container service:
```diff
- auth();
+ $authFactory;
- auth($guard);
+ $authFactory->guard($guard);
```

## Cookies

Class to DI:
```php
Illuminate\Contracts\Cookie\Factory $cookieFactory
```

Instead of these functions, use these methods on the container service:
```diff
- cookie();
+ $cookieFactory;
- cookie(... $arguments);
+ $cookieFactory->make(... $arguments);
```

## Encryption

Class to DI:
```php
Illuminate\Contracts\Encryption\Encrypter $encrypter
```

Instead of these functions, use these methods on the container service:
```diff
- encrypt();
+ $encrypter->encrypt();
- decrypt();
+ $encrypter->decrypt();
```

## Bcrypt

Class to DI:
```php
Illuminate\Contracts\Hashing\Hasher $hashManager
```

Instead of this function, use the method on the container service:
```diff
- bcrypt();
+ $hashManager->driver('bcrypt')->make();
```

## Session

Class to DI:
```php
Illuminate\Session\SessionManager $sessionManager
```

Instead of these functions, use these methods on the container service:
```diff
- session();
+ $sessionManager;
- session($key);
+ $sessionManager->get();
- csrf_token();
+ $sessionManager->token();
- csrf_field();
+ new HtmlString('<input type="hidden" name="_token" value="' . $sessionManager->token() . '">')
```

## Broadcasting

Class to DI:
```php
Illuminate\Contracts\Broadcasting\Factory $broadcastManager
```

Instead of this function, use the method on the container service:
```diff
- broadcast();
+ $broadcastManager->event();
```

## Jobs

Class to DI:
```php
Illuminate\Contracts\Bus\QueueingDispatcher $dispatcher
```

Instead of this function, use the method on the container service:
```diff
- dispatch();
+ $dispatcher->dispatch();
```

## Events

Class to DI:
```php
Illuminate\Contracts\Events\Dispatcher $eventDispatcher
```

Instead of this function, use the method on the container service:
```diff
- event();
+ $eventDispatcher->dispatch();
```

## Policies

Class to DI:
```php
Illuminate\Contracts\Auth\Access\Gate $gate
```

Instead of this function, use the method on the container service:
```diff
- policy();
+ $gate->getPolicyFor();
```

## Views

Class to DI:
```php
Illuminate\Contracts\View\Factory $viewFactory
```

Instead of this function, use the method on the container service:
```diff
- view();
+ $viewFactory->make();
```

## Validation

Class to DI:
```php
Illuminate\Contracts\Validation\Factory $validationFactory
```

Instead of this function, use the method on the container service:
```diff
- validator();
+ $validationFactory->make();
```

## Cache

Class to DI:
```php
Illuminate\Contracts\Cache\Factory $cacheManager
```

Instead of this function, use the method on the container service:
```diff
- cache();
+ $cacheManager->get();
```

## Environment Variables

```diff
- env();
```

The env function relies on the contents of your .env.* files. But using this method [outside your configuration file causes behaviour changes when you cache your configuration](https://laravel.com/docs/9.x/configuration#configuration-caching){:target="_blank" rel="noreferrer noopener"}, so is discouraged. This is also why there is no DI alternative given here. Instead, set a config key for any environment variable you need and use the config function alternative instead.

## Models / Eloquent / All other static method calls

As static method calls should always return the value regardless of database contents, these are not actually static classes. We can replace them with DI'ed classes however;

For classes that can be resolved based on a current route parameter, [Laravel already has you covered with implicit model binding](https://laravel.com/docs/9.x/routing#implicit-binding){:target="_blank" rel="noreferrer noopener"}:
```diff
- public function get(int $userId)
-     $user = App\User::findOrFail($userId);
+ public function get(App\User $user)
```

But if you want to return a collection of objects or do anything else with Eloquent, you can DI the model class instead on use that to query;

Class to DI:

```php
App\User $user
```

```diff
- App\User::all();
+ $user->all();
```

I hope this helps you in any way. If I missed anything here or if there is an incorrect section above. please let me know in an issue on the repo of this blog on github.
