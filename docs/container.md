---
layout: page
title: The Container Component
permalink: /docs/container/
---

### Getting started

Once bootstrapped, the next step is to create a container for your application and register services into it. You should install the container instance into the global variable (more on that below) or pass it along to your application components so that they can resolve dependencies from it.

{% highlight js %}
require('enclosure').prelude();

var Container = use('Chromabits/Container/Container'),
    Wrap = use('Chromabits/Container/Wrap');

var app = new Container();
{% endhighlight %}

Defining a singleton service:

{% highlight js %}
var myServiceInstance = new ServiceOne();

app.instance('ServiceOne', myServiceInstance);
{% endhighlight %}

Using a service:

{% highlight js %}
var myService = app.make('ServiceOne');
{% endhighlight %}

### Installing

It is possible to install the container into an object such as the `global` object. This makes it easier to access the container throughout your application:

{% highlight js %}
app.installTo(global)

// At this point, the container is available globally
var myService = container.make('ServiceOne');
{% endhighlight %}

Enclosure does not install itself automatically to the global context since there might be cases where you want to avoid using global variables or another library uses the required variables.

## Dependency Injection Methods:

There are many ways of defining dependencies on your services with Enclosure:

- [Constructor Dependency Injection](#constructor-dependency-injection)
- [Factories](#factories)
- [Wraps](#wraps)

### Constructor dependency injection

Defining a service using constructor dependency injection:

{% highlight js %}
// Enclosure's Container will use introspection to look
// at the constructor's parameters and try to resolve
// services with matching names
var ServiceTwo = function (ServiceOne) {
    // Keep references to service instances
    this.one = ServiceOne;
};

container.bind('ServiceTwo', ServiceTwo);

var instanceTwo = container.make('ServiceTwo');
{% endhighlight %}

If you would like to access the container directly within a service declare `EnclosureContainer` as a dependency:

{% highlight js %}
var ServiceThree = function (EnclosureContainer) {
    this.app = EnclosureContainer;
    
    // Then you can call the container from within your class
    // ex: var someService = this.app.make('SomeService');
};

container.bind('ServiceThree', ServiceThree);
{% endhighlight %}

#### Limitations

EcmaScript does not allow slashes `/` as part of a parameter name, so as a workaround Enclosure's container can replace underscores `_` with `/` when resolving dependencies in constructors.

Example: `Chromabits_Container_Container` becomes `Chromabits/Container/Container`

If this syntax seems ugly or you need to minify your code, you can always just use Wraps (defined below).

### Factories

Factory functions allow you to tell the container how to build a service for you. They are executed every time the container needs that service.

{% highlight js %}
container.factory('ServiceFour', function (container) {
    var instanceOne = container.make('ServiceOne');
    
    // In this example ServiceFour requires a configuration object
    // as the first parameter of the constructor, so constructor DI does
    // not work. The factory function allows us to tell the container
    // how to build this service
    return new ServiceFour({ sayHello: true }, instanceOne);
});
{% endhighlight %}

### Wraps

Wraps are delicious. They are a simple construct for coupling dependency definitions and a factory function for building a service instance.

Wraps are useful when constructor dependency injection is not applicable, which can happen when your class requires parameters in the constructor which are not services, or when your code is minified (parameters names are lost).

Defining a "wrapped service":

{% highlight js %}
var myServiceWrap = new Wrap(
    ['DependencyOne', 'DependencyTwo'], 
    function (container, DependencyOne, DependencyTwo) {
        // When this function is called, dependencies will be passed
        // as arguments. In this case, we just pass them along to the
        // constructor of our service so that we can use them inside it
        return new ServiceOne(DependencyOne, DependencyTwo, 'some config string');
});

container.bind('ServiceOne', myServiceWrap);
{% endhighlight %}

## Shared Services

Shared services are services that become singletons once they are built. This means that if the service is never requested it will never be instantiated. However, if it is, the instance will be cached and returned on every subsequent `make` call. 

Shared services can be defined by providing a third parameter to `container.bind` and `container.factory`:

{% highlight js %}
container.bind('ServiceOne', myServiceWrap, true);

container.factory('ServiceFour', function () { ... }, true);
{% endhighlight %}
