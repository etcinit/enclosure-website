---
layout: page
top: true
title: Documentation
permalink: /docs/
---

### Before you start

Please read the [foreword](/docs/foreword). There are some important facts about Enclosure on it.
_Enclosure might not be for everyone, please consult your doctor before using it :D_

## Summary

- [Foreword](/docs/foreword)
- [Roadmap](/docs/roadmap/)
- [Getting Started](/start/)
- [Components](#components)
    - [Bootstrapper](/docs/bootstrapper/)
    - [Container](/docs/container/)
    - [Application](/docs/application/)
    - [Loader](/docs/loader/)

## About

Enclosure is a library that aims to make developing complex Node.js applications easier. It provides a simple foundation for getting an Inversion of Control container and Module Loading system in your application.

The IoC container acts as a centralized repository of service singletons and factory functions. Whenever you define a service in your application, you register it in the container, and when you need to use it you ask the container to construct an instance or pass along a reference to the singleton.

While you could implement similar functionality by defining all services in a global object, the IoC container has the added benefit that it can perform Dependency Injection and it can resolve aliases. Dependency Injection is very useful for testing since it allows you to quickly replace a component of your application with a mock or stub for testing, without having to change the code that uses it at all.

The second half of Enclosure consists of a module loading system. This sounds redundant since Node.js already includes `require`. However, as
applications grow more complicated and the number .js files in your source folder keeps growing, requiring files from each other creates a very spaghetti-like situation which makes it really hard to refactor code.

The Loader requires you to structure your project in a very specific way (inspired by PHP's PSR standards), but once it is setup, it allows you include files using an absolute path format rather than using relative paths. This allows you to quickly move files around in your project without having to worry too much about broken `require()`s.

Additionally, Enclosure provides other utility components such as an extended container with support for Service Providers and automated Bootstrapper.

### Components

- **Bootstrapper Component:** The bootstrapper component is capable of setting up a container, an application, service providers, and a loader with a single method call.
- **Container Component:** There is an almost complete implementation of the IoC container. Services can be defined through a binding, a factory. Services can also be singletons, or stored as singletons once they are resolved (Shared services). Services can define dependencies through their constructors or a Wrap. Circular dependencies can be detected. The container can also fallback to the loader if a service cannot be resolved, and even apply dependency injection to this method.
- **Application Component:** Is an extension of the Container, and is able to register and boot Service Providers, which can be referenced just by their class name thanks to the Loader component.
- **Loader Component:** Is capable of resolving class constructors and modules from a full namespace path (`Name/Space/Class`). Currently the two possible class map generators can generate class maps for directories and for Enclosure itself. The component is flexible enough for future mappers to be added in the future.
