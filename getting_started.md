---
layout: page
title: Getting started
top: true
permalink: /start/
---

### Before you start

Please read the [foreword](/docs/foreword). There are some important facts about Enclosure on it.
_Enclosure might not be for everyone, please consult your doctor before using it :D_

### Installation

Installation is simple. Just require the library using npm:

{% highlight sh %}
$ npm install enclosure
{% endhighlight %}

and then reference it from your code:

{% highlight js %}
var enclosure = require('enclosure');
{% endhighlight %}

### Compatibility

Currently, Enclosure only officially supports Node.js environments, mostly due to the dependency on the filesystem. It should be possible to the container component on browser applications by using libraries like Browserify.

Additionally, when installing Enclosure to the global scope, the following variables are reserved: `container`, `use`

# Tutorial

After installing, you can follow the [tutorial](/tutorial/)
