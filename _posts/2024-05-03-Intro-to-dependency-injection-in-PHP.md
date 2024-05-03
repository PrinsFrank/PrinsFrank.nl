---
title: Intro to Dependency Injection in PHP
subtitle: creating a simple DI service from scratch
Description:
categories: [development]
tags: []
readtime: 
---

**When talking about design patterns in programming, it's often difficult to talk about a single pattern in isolation. When looking for articles that explain Dependency Injection, I realised that this is one of those topics. Patterns like LSP, Service Locators, Service Containers and others usually get talked about. But Dependency Injection can be much more simple than that. Let's take away some of that magic!**

Let's start with three simple classes: A, B and C;

```php
class A {
    public function __construct(
        private readonly B $b, 
        private readonly C $c,
    ) {}
}
```

```php
class B {
    public function __construct(
        private readonly C $c
    ) {}
}
```

```php
class C {}
```

If you want to get an instance of class A, if you manually construct it you'll have to pass all of its arguments manually:

```php
$a = new A(new B(new C()), new C());
```

Even in this simple example, where A relies on B and C, and B relies on class C, it already becomes quite complicated to construct a deep dependency tree. And when an application becomes more complex, so does the tree of dependencies.

But looking at these classes, all the dependencies for them are clearly defined in the constructor. That's were dependency injection comes in. We can actually quite easily build our own Dependency Injector!

Assuming that we have PHPUnit already set up, I usually start with a simple Unit test for the functionality I expect to support. In this case, let's use the classes and example from above, and let's make sure that our new Injector acts the same;

```php
class DependencyInjectorTest extends TestCase {
    public function testGet(): void {
        static::assertEquals(
            new A(
                new B(
                    new C(),
                ),
                new C(),
            ),
            (new DependencyInjector())->get(A::class)
        );
    }
}
```

You can see I decided on the classname 'DependencyInjector', and that I expect a class-string as argument. We can't give it an object, as then we would have already had to call the constructor, which we want the dependencyInjector to call.

Moving on to the service itself. For now we only add one method. As PHP doesn't have a native type for class-strings, we have to use 'string' as the parameter type definition. As we want to add support for all object types, we'll set the return type to 'object';

```php
class DependencyInjector {
    public function get(string $FQN): object {
    }
}
```

I am a big fan of explicit types, but as PHP doesn't natively support Generics we'll have to rely on DocBlocks to provide those. We can tell our IDE and static analysis a lot more about the type of string and object that we'll return. Below we're telling them that if a parameter is passed with a class-string for an object, we will return an instance of that object. In other words: If I call this method with 'A::class', it will return an object of 'A';

```php
class DependencyInjector {
    /**
     * @template T of object
     * @param class-string<T> $FQN
     * @return object<T>
     */
    public function get(string $FQN): object {
    }
}
```

Now It's time to actually build some functionality, and run our Unit test from above after every chance. Before anything, even though we say to our tools that the string is a class-string, we have to verify it's actually the case;

```php
class DependencyInjector {
    /**
     * @template T of object
     * @param class-string<T> $FQN
     * @return object<T>
     */
    public function get(string $FQN): object {
        if (class_exists($FQN) === false) {
            throw new InvalidArgumentException('$FQN should be a class-string');
        }
    }
}
```

Next, if the constructor for that class does not exist we don't need to pass it any parameters, so we can simply call the constructor immediately;

```php
class DependencyInjector {
    /**
     * @template T of object
     * @param class-string<T> $FQN
     * @return object<T>
     */
    public function get(string $FQN): object {
        if (class_exists($FQN) === false) {
            throw new InvalidArgumentException('$FQN should be a class-string');
        }

        if (method_exists($FQN, '__construct') === false) {
            return new $FQN;
        }
    }
}
```

Now comes the fun part: Collecting all the parameters for the constructor. Let's start with an array of parameters that we use to call the constructor. Using the splat operator (...), we can then pass all of them to the constructor;

```php
class DependencyInjector {
    /**
     * @template T of object
     * @param class-string<T> $FQN
     * @return object<T>
     */
    public function get(string $FQN): object {
        if (class_exists($FQN) === false) {
            throw new InvalidArgumentException('$FQN should be a class-string');
        }

        if (method_exists($FQN, '__construct') === false) {
            return new $FQN;
        }

        $params = [];
        // Create parameters

        return new $FQN(...$params);
    }
}
```

To get the type of the constructor parameters, we have to use the 'getParameters' method on the 'ReflectionMethod' class:

```php
class DependencyInjector {
    /**
     * @template T of object
     * @param class-string<T> $FQN
     * @return object<T>
     */
    public function get(string $FQN): object {
        if (class_exists($FQN) === false) {
            throw new InvalidArgumentException('$FQN should be a class-string');
        }

        if (method_exists($FQN, '__construct') === false) {
            return new $FQN;
        }

        $params = [];
        foreach ((new ReflectionMethod($FQN, '__construct'))->getParameters() as $parameter) {
            // Create parameter
        }

        return new $FQN(...$params);
    }
}
```

Let's take a step back and think about all constructors that are possible. There are some we won't be able to handle because we don't know what to pass it. 
```php
public function __construct($foo);        // Without type
public function __construct(mixed $foo);  // With mixed type
public function __construct(string $foo); // With string type
public function __construct(A|B $foo);    // With Union type of classes
public function __construct(A&C $foo);    // With Intersection type of classes
```

To gracefully handle all of these scenario's, we'll check if there's any uncertainty about the parameter. First, let's start with parameters that don't have a type:

```php
class DependencyInjector {
    /**
     * @template T of object
     * @param class-string<T> $FQN
     * @return object<T>
     */
    public function get(string $FQN): object {
        if (class_exists($FQN) === false) {
            throw new InvalidArgumentException('$FQN should be a class-string');
        }

        if (method_exists($FQN, '__construct') === false) {
            return new $FQN;
        }

        $params = [];
        foreach ((new ReflectionMethod($FQN, '__construct'))->getParameters() as $parameter) {
            $parameterType = $parameter->getType();
            if ($parameterType === null) {
                throw new UnresolvableDependencyException(sprintf('Parameter %s doesn\'t have a type defined, so it cannot be resolved', $parameter->getName()));
            }
            // Create parameter
        }

        return new $FQN(...$params);
    }
}
```

Next, let's check all non-class types. The 'ReflectionType' class has a method called 'isBuiltIn', which according to the [PHP docs is "any type that is not a class, interface, or trait."](https://www.php.net/manual/en/reflectionnamedtype.isbuiltin.php){:target="_blank" rel="noreferrer noopener"}. If the parameter is a Union or Intersection type, the $parameterType will be an instance of 'ReflectionUnionType' or 'ReflectionIntersectionType' so we can check for that as well. We don't support interfaces right now either, so we can check if the type is an interface using 'interface_exists'. And lastly we can't construct traits, so we check that using 'trait_exists'. Now that we have most edge cases covered, our method looks like this;

```php
class DependencyInjector {
    /**
     * @template T of object
     * @param class-string<T> $FQN
     * @return object<T>
     */
    public function get(string $FQN): object {
        if (class_exists($FQN) === false) {
            throw new InvalidArgumentException('$FQN should be a class-string');
        }

        if (method_exists($FQN, '__construct') === false) {
            return new $FQN;
        }

        $params = [];
        foreach ((new ReflectionMethod($FQN, '__construct'))->getParameters() as $parameter) {
            $parameterType = $parameter->getType();
            if ($parameterType === null) {
                throw new UnresolvableDependencyException(sprintf('Parameter %s of %s doesn\'t have a type defined, so it cannot be resolved', $parameter->getName(), $FQN));
            }

            if ($parameterType->isBuiltin() 
                || $parameterType instanceof ReflectionUnionType 
                || $parameterType instanceof ReflectionIntersectionType 
                || interface_exists($parameterType->getName()) 
                || trait_exists($parameterType->getName())) {
                throw new UnresolvableDependencyException(sprintf('Parameter %s of %s has an unsupported type. Only class-string of concrete classes can be resolved', $parameter->getName(), $FQN));
            }

            // Create parameter
        }

        return new $FQN(...$params);
    }
}
```

One last thing to do here: Actually create the parameter class. But wait, we already have a method for that: the 'get' method does exactly what we want to construct the underlying class, including resolving the parameters for it;

```php
class DependencyInjector {
    /**
     * @template T of object
     * @param class-string<T> $FQN
     * @return object<T>
     */
    public function get(string $FQN): object {
        if (class_exists($FQN) === false) {
            throw new InvalidArgumentException('$FQN should be a class-string');
        }

        if (method_exists($FQN, '__construct') === false) {
            return new $FQN;
        }

        $params = [];
        foreach ((new ReflectionMethod($FQN, '__construct'))->getParameters() as $parameter) {
            $parameterType = $parameter->getType();
            if ($parameterType === null) {
                throw new UnresolvableDependencyException(sprintf('Parameter %s of %s doesn\'t have a type defined, so it cannot be resolved', $parameter->getName(), $FQN));
            }

            if ($parameterType->isBuiltin() 
                || $parameterType instanceof ReflectionUnionType 
                || $parameterType instanceof ReflectionIntersectionType 
                || interface_exists($parameterType->getName()) 
                || trait_exists($parameterType->getName())) {
                throw new UnresolvableDependencyException(sprintf('Parameter %s of %s has an unsupported type. Only class-string of concrete classes can be resolved', $parameter->getName(), $FQN));
            }

            $params[] = $this->get($parameterType->getName());
        }

        return new $FQN(...$params);
    }
}
```

There are a few edge cases here that I'm purposefully not implementing. What if there are circular dependencies for example? (A requires B but B also requires A). These are all implementation details, that don't add to understanding how dependency injection works. 

I hope this naive implementation of a dependency injector takes away some of the mystery of DI. If you want to take it to the next level and rely on interfaces instead of concrete classes as parameter types, I can write about that in a follow-up post. Let me know!
