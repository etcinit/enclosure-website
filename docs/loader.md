---
layout: page
title: The Loader Component
permalink: /docs/loader/
---

### Class Maps

Enclosure has the concept of a Class Map, which is a simple class capable of mapping a full class name into a function or a JavaScript file.

In most cases, you'll probably won't have to deal with ClassMaps directly, but instead use Mappers (explained below).

#### Class names

The class name convention for Enclosure is borrowed from PHP namespaces, with foward slashes instead of back-slashes as path separators.

_Valid:_

- `Example/Services/Mailer`
- `/Example/App`
- `/Server`
- `Database`

_Invalid:_

- `\..\App`
- `app.js`
- `Example/++/Mailer`
- and many more...

#### EnclosureClassMap

A class map with all Enclosure classes is available as `Chromabits/Mapper/EnclosureClassMap`. This is the map used by the bootstrap environment. However, as soon as you setup your own class loader, Enclosure classes will not be available anymore unless you add this map into your loader.

### Mappers

The first step towards automatic class loading is to use a Mapper. In Enclosure, a Mapper is basically just a factory capable of creating a class map. If you would like to create your own, just extend the `AbstractMapper` class. However, most people will probably just want to use the `DirectoryMapper` which generates a class map by looking at a directory structure.

#### DirectoryMapper

Consider the following project structure:

```
src/
    app.js
    Mailer.js
    Database/
        Post.js
    Controllers/
        ContactController.js
```

__src/app.js:__

```js
require('enclosure').bootstrap();

var DirectoryMapper = use('Chromabits/Mapper/DirectoryMapper');

var mapper = new DirectoryMapper(__dirname);

// Generate the class map
var map = mapper.generate();
```

The generated map in this case will contain the following files:

- `app`
- `Mailer`
- `Database/Post`
- `Controllers/ContactController`

### Containers and Loaders

The last piece of the puzzle is Loaders. Loaders are just classes that group a bunch of class maps together and are able to tell if they can resolve a certain class:

```js
require('enclosure').prelude();

var Container = use('Chromabits/Container/Container'),
    Loader = use('Chromabits/Loader/Loader'),
    DirectoryMapper = use('Chromabits/Mapper/DirectoryMapper'),
    EnclosureMap = use('Chromabits/Mapper/EnclosureClassMap');

// Setup class autoloading
var loader = new Loader();
var mapper = new DirectoryMapper(__dirname);

loader.addMap(mapper.generate()); // Adds app classes
loader.addMap(EnclosureMap); // Adds enclosure classes

loader.has('Chromabits/Container/Application') 
>>> true

loader.has('Example/Fake') 
>>> false
```

A Container can be made more useful by attaching a Loader to it. This will allow it to resolve classes out of the loader if there are no services implementing them:

```js
// Start the service container
var container = new Container();

container.setLoader(loader);

container.installTo(global);
```

__IMPORTANT:__ `setLoader` should be called before `installTo`. Otherwise, the `use()` is not defined/replaced in the target object.

### Putting it all together

Below is a full example of the previous components:

__src/app.js:__

```js
require('enclosure').prelude();

var Container = use('Chromabits/Container/Container'),
    Loader = use('Chromabits/Loader/Loader'),
    DirectoryMapper = use('Chromabits/Mapper/DirectoryMapper'),
    EnclosureMap = use('Chromabits/Mapper/EnclosureClassMap');

// Setup class autoloading
var loader = new Loader();
var mapper = new DirectoryMapper(__dirname);

loader.addMap(mapper.generate());
loader.addMap(EnclosureMap);

// Start the service container
var container = new Container();

container.setLoader(loader);
container.installTo(global);

// World should be resolved automatically in Hello
// Also, note that we never registered Hello or World as services
var hello = container.make('Hello');

console.log(hello.say());
>>> 'hello world'
```

__src/Hello.js:__

```js
var Hello = function (World) {
    this.text = 'hello' + World.say();
};

Hello.prototype.say = function () {
    return this.text;
};

module.export = Hello;
```

__src/World.js:__

```js
var World = function () {};

World.prototype.say = function () {
    return 'world';
};

module.exports = World;
```

### use() vs require()

If you installed the container globally (`installTo(global)`), you can use the `use()` function as a replacement of `require()`. This function is also available as `container.use()`.

The main difference between `require` and `use` is that `use` uses your container's loader to find classes. This has the benefit that you can require classes and objects using an absolute path rather than a relative one.

For example, consider the following structure:

```
src/
    Mailer.js
    Controllers/
        ContactController.js
```

If we needed to use `Mailer` inside the contact controller, we would normally write something like:

```js
var Mailer = require('../Mailer');
```
However, with Enclosure we can use the following syntax (Assuming you setup a class mapper with `src` as the base directory):

```js
var Mailer = use('Mailer');
```

If we move the controller file into another directory, such as `src/Controllers/Front/`:

```js
// This now breaks:
var Mailer = require('../Mailer');
// and has to be changed to this:
var Mailer = require('../../Mailer');

// However, this still works:
var Mailer = use('Mailer');
```

Please note that this function does not construct an instance. It actually returns the constructor function (or whatever you exported in your module). If you would like to create an instance, see automatic construction below.

### Automatic construction

As shown in the complete example above, the Container is capable of using a Loader to try to resolve services that are not explicitly bound. Dependency Injection is also performed during this process. So you can use classes with dependencies as services without having to register them. 

Continuing the previous example:

```js
// This gets the constructor function
var Mailer = use('Mailer');
>>> [Function]

// This gets an instance
var mailer = container.make('Mailer');
>>> Object
```
