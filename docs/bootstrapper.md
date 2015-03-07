---
layout: page
title: The Bootstrapper Component
permalink: /docs/bootstrapper/
---

### First steps

If you are developing an application with Enclosure (not a library), a good place to start to use the Bootstrapper class. This class can automatically setup Enclosure for you. All you need to do is add some extra information to your `package.json` and call the library. 

Example `index.js`:

{% highlight js %}
// This creates, setups and runs an application container
require('enclosure').boot();
{% endhighlight %}

Example `package.json`:

{% highlight js %}
{
    // ...
    "entrypoint": "MyBlog/Console/App",
    "providers": [
        "MyBlog/Http/Providers/RouterServiceProvider",
        "MyBlog/Mail/MailServiceProvider"
    ],
    "autoload": {
        "roots": ["src"]
    }
    // ...
}
{% endhighlight %}

Each of these keys will be explained more in detail in the sections below. However, note that **none of these are required** for `boot()` to work properly, although you will probably want to use them to include your own classes.

A more comprehensive explanation of the package.json extension is available [here](/docs/package/)

The boot function can also take additional options:

{% highlight js %}
// You are not required to use package.json
require('enclosure').boot({
    metadata: '/path/to/metadata.json'
});

// You can also just provide the metadata directly
require('enclosure').boot({
    metadata: {
        "entrypoint": "MyBlog/Console/App",
        "providers": [
            "MyBlog/Http/Providers/RouterServiceProvider",
            "MyBlog/Mail/MailServiceProvider"
        ],
        "autoload": {
            "roots": ["src"]
        }
    }
});

// You can also specify where to install the container
// See the section on the Container component for a description
// of what this actually means
require('enclosure').boot({
    installTo: someObject
});
{% endhighlight %}

#### Global variables

After running the bootstrapper, the container should be available under `container` global variable. The loader can be used through the `use()` global variables. Unless you provided a different install context through the `installTo` config option.

#### Project Structure

While it is not enforced in any way, most examples will assume the following project structure:

{% highlight js %}
src/
    Example/
        App.js
        Controllers/
            IndexController.js
            ...
        Providers
            RandomServiceProvider.js
            ...
        ...
test/
    ...
index.js
package.json
{% endhighlight %}

The matching metadata configuration is:

{% highlight js %}
//...
"entrypoint": "Example/App",
"providers": ["Example/Providers/RandomServiceProvider"],
"autoload": {
    "roots": ["src"]
}
//...
{% endhighlight %}

#### The Entrypoint

So you have probably noticed the `entrypoint` configuration key. This tells the bootstrapper where to go next once your application has been booted. The entrypoint is treated as any other service and will have it dependencies resolved during instantiation.

Example `App.js`:

{% highlight js %}
// Dependency injection
var App = function (ServiceOne, ServiceTwo) {
    this.serviceOne = ServiceOne;
    this.serviceTwo = ServiceTwo;
};

// Command line arguments are passed in for convenience
App.prototype.main = function (args) {
    // This is where you would start setting up your app,
    // launch servers, etc
};

module.exports = App;
{% endhighlight %}

`main(args)` gets called once `enclosure.boot()` has finished setting up the environment.

### Prelude Environment

If you want more control on how Enclosure is setup, you can use the Prelude environment. You will have to manually setup the Container and Loader components.

After installation, the first thing you'll probably want to do is use one of Enclosure's classes. On previous versions, these were inside the enclosure object itself (`enclosure.Container`). However, since newer versions include more classes, this method was dropped since it requires every file in enclosure when it is required, which can be slow.

Instead, Enclosure makes use of it's own Loader component (the `use()` function) for getting classes. The initial prelude environment is only capable of resolving classes that are inside the library. 

To initialize the bootstrap environment, just call the following function before any call to `use()`:

{% highlight js %}
require('enclosure').prelude();

// Now you can use Enclosure's classes by using use()
var Container = use('Chromabits/Container/Container');
var instance = new Container();

// You can also specify where to install the environment to if you 
// prefer to keep the global context clean
require('enclosure').preludeTo(someObject);
var Container = someObject.use(...);
{% endhighlight %}

See _The Loader Component_ below for more instructions on how to setup the Loader to load your own classes and how to use the `use()` function.
