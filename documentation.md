---
layout: page
top: true
title: Documentation
permalink: /docs/
---

### Before you start

Please read the [foreword](/docs/foreword). There are some important facts about Enclosure on it.
_Enclosure might not be for everyone, please consult your doctor before using it :stuck_out_tongue_winking_eye:_

## Summary

- [Foreword](/docs/foreword)
- [Roadmap](/docs/roadmap/)
- [Getting Started](/start/)
- [Components](#components)
    - [Bootstrapper](/docs/bootstrapper/)
        - [First steps](/docs/bootstrapper/#first-steps)
        - [The entry point](/docs/bootstrapper/#the-entrypoint)
        - [The Prelude Environment](/docs/bootstrapper/#prelude-environment)
    - [Container](/docs/container/)
        - [Constructor dependency injection](/docs/container/#constructor-dependency-injection)
        - [Factories](/docs/container/#factories)
        - [Wraps](/docs/container/#wraps)
        - [Shared Services](/docs/container/#shared-services)
    - [Application](/docs/application/)
        - [Service Providers](/docs/application/#service-providers)
    - [Loader](/docs/loader/)
- Organizing your project
- [Package.json extensions](/docs/package/)

## About

Enclosure is a library that aims to make developing complex Node.js applications easier. It provides a simple foundation for getting an Inversion of Control container and Module Loading system in your application.

#### Why Inversion of Control?

The IoC container acts as a centralized repository of service singletons and factory functions. Whenever you define a service in your application, you register it in the container, and when you need to use it you ask the container to construct an instance or pass along a reference to the singleton.

While you could implement similar functionality by defining all services in a global object, the IoC container has the added benefit that it can perform Dependency Injection and it can resolve aliases. Dependency Injection is very useful for testing since it allows you to quickly replace a component of your application with a mock or stub for testing, without having to change the code that uses it at all.

Recommended articles and projects:

- [wire](https://github.com/cujojs/wire) by cujojs
- [electrolyte.js](https://github.com/jaredhanson/electrolyte): Lightweight IoC container for Node.js
- [Dependency Injection](https://en.wikipedia.org/wiki/Dependency_injection)
- [Inversion of Control Containers and the Dependency Injection pattern](http://martinfowler.com/articles/injection.html) by Martin Fowler

#### Why a module loader?

The second half of Enclosure consists of a module loading system. This might initially sound redundant since Node.js already includes `require` and any library already works with it.

However, one case that `require()` does not cover very well is your application's own code.

As applications grow more complicated and the number of JavaScript files in your source folder keeps growing, and requiring files from each other using relative paths creates a very spaghetti-like situation which makes it really hard to refactor code.

Some solutions have popped up trying to solve this problem but most of them seemed very hackish: https://gist.github.com/branneman/8048520. Enclosure attempts to make this more structured and organized.

Example:

{% highlight js %}
var service = require('../../../service.js');
// vs
var service = use('MyApp/Support/Service');
{% endhighlight %}

The Loader requires you to structure your project in a very specific way (inspired by PHP's PSR standards), but once it is setup, it allows you include files using an absolute path format rather than using relative paths. This allows you to quickly move files around in your project without having to worry too much about broken `require()`s.

#### Why both?

When both components are setup, the Container is capable of falling back to the Loader if it cannot resolve a service. The Loader will try to find a file with the name of the service and load it, then the Container attempts to create a new instance and resolve dependencies.

This means that you can potentially avoid explicitly binding services into the container if all dependencies can be resolved automatically.

#### Extras

Additionally, Enclosure provides other utility components such as an extended container with support for Service Providers and automated Bootstrapper.

### Seeing is better than reading (sometimes):

#### Example Project:
Check out the example project for a near comprehensive use of all the features in Enclosure. The project is located under the `example/` directory. Please note that this project is written in EcmaScript 6 (ES6).

You can find the source here: [https://github.com/etcinit/enclosure/tree/master/example](https://github.com/etcinit/enclosure/tree/master/example)

#### Other projects using Enclosure:

- [Phabricator-Slack Bridge](https://github.com/etcinit/phabricator-slack-feed)

### Component overview:

- [**Bootstrapper Component:**](/docs/bootstrapper/) The bootstrapper component is capable of setting up a container, an application, service providers, and a loader with a single method call.
- [**Container Component:**](/docs/container) There is an almost complete implementation of the IoC container. Services can be defined through a binding, a factory. Services can also be singletons, or stored as singletons once they are resolved (Shared services). Services can define dependencies through their constructors or a Wrap. Circular dependencies can be detected. The container can also fallback to the loader if a service cannot be resolved, and even apply dependency injection to this method.
- [**Application Component:**](/docs/application/) Is an extension of the Container, and is able to register and boot Service Providers, which can be referenced just by their class name thanks to the Loader component.
- [**Loader Component:**](/docs/loader/) Is capable of resolving class constructors and modules from a full namespace path (`Name/Space/Class`). Currently the two possible class map generators can generate class maps for directories and for Enclosure itself. The component is flexible enough for future mappers to be added in the future.
