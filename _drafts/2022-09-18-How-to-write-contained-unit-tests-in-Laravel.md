---
title: How to write contained unit tests in Laravel
subtitle: And how you can make your code more testable and your tests run faster
Description:
categories: [development]
tags: []
readtime:
---

## Paths

```diff
+ \Illuminate\Contracts\Foundation\Application $application
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

```diff
+ \Illuminate\Contracts\Foundation\Application $application
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
```diff
+ \Illuminate\Translation\Translator $translator
- __();
+ $translator->translate();
- trans();
+ $translator->translate();
- trans_choice();
+ $translator->choice();
```

## Routes
```diff
+ \Illuminate\Contracts\Routing\UrlGenerator $urlGenerator
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

```diff
+ \Illuminate\Routing\Redirector $redirector
- redirect();
+ $redirector->to();
- to_route();
+ $redirector->route();
- back();
+ $redirector->back();
```

## Environment Variables

```diff
- env();
```

@TODO ADD SECTION

## Configuration Variables

```diff
+ \Illuminate\Config\Repository $configRepository
- config();
+ $configRepository->all();
- config($key);
+ $configRepository->get($key);
```

## Logging
```diff
+ \Illuminate\Log\LogManager $logManager
- logger();
+ $logManager;
- logger($message);
+ $logManager->debug($message);
- info($message);
+ $logManager->info($message);
```

## Exception Reporting
```diff
+ Illuminate\Contracts\Debug\ExceptionHandler $exceptionHandler
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
```diff
+ \Illuminate\Http\Request $request
- request();
+ $request
- old();
+ $request->old();
```

## Response
```diff
+ \Illuminate\Contracts\Routing\ResponseFactory $responseFactory
- response();
+ $responseFactory;
- response($content);
+ $responseFactory->make($content);
```

## Mix
```diff
+ \Illuminate\Foundation\Mix $mix
- mix();
+ $mix();
```

## Auth
```diff
+ \Illuminate\Contracts\Auth\Factory $authFactory
- auth();
+ $authFactory;
- auth($guard);
+ $authFactory->guard($guard);
```

## Cookies
```diff
+ \Illuminate\Contracts\Cookie\Factory $cookieFactory
- cookie();
+ $cookieFactory;
- cookie(... $arguments);
+ $cookieFactory->make(... $arguments);
```

## Encryption
```diff
+ \Illuminate\Encryption\Encrypter $encrypter
- encrypt();
+ $encrypter->encrypt();
- decrypt();
+ $encrypter->decrypt();
```

## Bcrypt
```diff
+ \Illuminate\Hashing\HashManager $hashManager
- bcrypt();
+ $hashManager->driver('bcrypt')->make();
```

## Session
```diff
+ \Illuminate\Session\SessionManager $sessionManager
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
```diff
+ \Illuminate\Broadcasting\BroadcastManager $broadcastManager
- broadcast();
+ $broadcastManager->event();
```

## Jobs
```diff
+ \Illuminate\Bus\Dispatcher $dispatcher
- dispatch();
+ $dispatcher->dispatch();
```

## Events
```diff
+ \Illuminate\Events\Dispatcher $eventDispatcher
- event();
+ $eventDispatcher->dispatch();
```

## Policies
```diff
+ \Illuminate\Contracts\Auth\Access\Gate $gate
- policy();
+ $gate->getPolicyFor();
```

## Views
```diff
+ \Illuminate\Contracts\View\Factory $viewFactory
- view();
+ $viewFactory->make();
```

## Validation
```diff
+ \Illuminate\Validation\Factory $validationFactory
- validator();
+ $validationFactory->make();
```

## Cache
```diff
+ \Illuminate\Cache\CacheManager $cacheManager
- cache();
+ $cacheManager->get();
```

## Self-contained global functions
```php
class_basename();
e();
preg_replace_array();
str();
blank();
class_uses_recursive();
collect();
dd();
dump();
blank();
filled();
method_field();
now();
optional();
retry();
tap();
throw_if();
throw_unless();
today();
trait_uses_recursive();
transform();
value();
with();
```

## Self-contained static methods
```php
Str::*();
Arr::*();
```
