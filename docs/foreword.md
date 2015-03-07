---
layout: page
title: Foreword
permalink: /docs/foreword/
---

## Introduction

Before you begin, take note that Enclosure has two big parts: The container component and the loader component. 
 
The [Container Component](/docs/container) works pretty much like other DI libraries for Javascript out there although it has some additional bits such as Service Providers and Wraps.

The [Loader Component](/docs/loader) is a module loader that allows you to specify paths from which modules can be loaded by specifying a full class name. For example: `use('MyBlog/Http/Controller')` will automatically `require()` the file `src/MyBlog/Http/Controller.js` if `src/` is set as a loader root directory.

This is the part of the library that might not be for everyone. It has a __VERY__ opinionated way of writing Javascript code. It really asks you to write code in a very specific way, and yes, the code written may reassemble OOP languages (see why below).

> NOTE: The Container Component should be stable enough for production applications. The Loader Component however is more experimental, and therefore should not be used in these kind of environments.

### Things that you will probably like:

- Complex and automated dependency injection
- Automated file loading and construction of classes
- No more relative paths on your Node.js code!
- Support for ES6
- Everything is in classes!

### Things that you will probably not like:

- Everything is in classes!
- It looks like somebody tried really hard to make JS look like PHP/Java
- It doesn't fit in well with other libraries (yet)
- It doesn't port to the web browser (yet)
- You have to write your project around it

### Why does it look like PHP/Java?

- It assumes there is one class per file
- The namespace tree is generated from the directory structure
- Files and directories are capitalized which is unusual on JS libraries
- There is the concept of an entry point, which is a class with a `main()` method
- Some code examples use ES6 syntax, which has syntactic sugar for classes and inheritance

### Why all this? Why not stick to JS standards?!?!

Because it's fun. Yes, it's that simple

### Decided to give it a try?

Check out the [Getting Started](/start/) guide and the [Documentation](/docs/)
