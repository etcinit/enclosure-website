---
layout: page
title: Package.json extensions
permalink: /docs/package/
---

The bootstrapper component is capable of parsing additional properties on your projects' package.json file in order to completely automate the bootstrapping of your application:

{% highlight sh %}
//...
"entrypoint": "Example/App",
"providers": ["Example/Providers/RandomServiceProvider"],
"autoload": {
    "roots": ["src"]
}
//...
{% endhighlight %}

### Properties:

#### entrypoint (string):

This specifies a class that the container should load after the application has been bootstrapped (service providers have been registered and booted).

The target class must be able to be instantiated by the container automatically through constructor DI, and it should contain a `main()` method that will be called after the instance is created.

#### providers (array):

A simple array of the service providers to bootstrap when setting up the application

#### autoload (object):

An options object for different autoloading mechanisms. Right now the only one supported is `roots`.

**roots**: An array of directories (relative to the package.json) to scan in order to build the namespace tree
