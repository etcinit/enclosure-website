---
layout: page
title: The Application Component
permalink: /docs/application/
---

## Service Providers

As your application gets more complex, you will probably want to break down how you register services into the container and perhaps perform some setup task as the application prepares to run. This is possible using Service Providers.

Enclosure includes an extension of the Container class called Application. Application is still a container but it has support for handing service providers

#### Example:

Note: The loader component is explained in the section below.

__src/app.js:__

{% highlight js %}
require('enclosure').prelude();

var Application = use('Chromabits/Container/Application'),
    Loader = use('Chromabits/Loader/Loader'),
    DirectoryMapper = use('Chromabits/Mapper/DirectoryMapper'),
    EnclosureMap = use('Chromabits/Mapper/EnclosureClassMap');

// Setup class autoloading
var loader = new Loader();
var mapper = new DirectoryMapper(__dirname);

loader.addMap(mapper.generate());
loader.addMap(EnclosureMap);

// Start the service container
var application = new Application();

application.setLoader(loader);
application.installTo(global);

// Register providers
container.addProvider('Providers/HelloServiceProvider');

// Register services and boot providers
container.register();
container.bootProviders();
{% endhighlight %}

__src/Providers/HelloServiceProvider.js:__

{% highlight js %}
var ServiceProvider = use('Chromabits/Container/ServiceProvider');

var HelloServiceProvider = function () {
    // Call parent constructor
    ServiceProvider.call(this, arguments);
}

HelloServiceProvider.prototype = new ServiceProvider();

// Here is where we register all services provided in this provider:
HelloServiceProvider.prototype.register = function (app) {
    app.bind('HelloWorld', function () {
        return 'Hello World';
    });
};

// Here we boot the services provided:
HelloServiceProvider.prototype.boot = function (app) {
    var hello = app.make('HelloWorld');
    
    // In this simple case we just call a simple function, but
    // you get the idea
    console.log(hello());
};

module.exports = HelloServiceProvider;
{% endhighlight %}

#### Example in ES6:

The previous example can be rewritten in EcmaScript 6 for a much simpler syntax:

__src/Providers/HelloServiceProvider.js:__

{% highlight js %}
var ServiceProvider = use('Chromabits/Container/ServiceProvider');

class HelloServiceProvider extends ServiceProvider
{
    register (app) {
         app.bind('HelloWorld', function () {
            return 'Hello World';
        });
    }
    
    boot (app) {
        var hello = app.make('HelloWorld');
    
        // In this simple case we just call a simple function, but
        // you get the idea
        console.log(hello());
    }
}

module.exports = HelloServiceProvider;
{% endhighlight %}

Enclosure works with transpilers like Babel, so you can use ES6 right now
