---
layout: docs-api
toc: toc-api-context.html
title: Context Store API
slug: context
---

**New in 0.19**

The Context Store API provides a pluggable way to configure where context data is
stored.

By default, Node-RED uses a [memory-based implementation](store/memory) of this API. It also provides
a [file-based implementation](store/localfilesystem).

To create a custom Context Store, a module should create that implements the [Store Module API](#store-module-api).

### Configuration

The `contextStorage` property in settings.js can be used to configure context storage.

It is an object with one or more named context store configurations.

{% highlight javascript %}
contextStorage: {
   default: {
       module:"memory",
       config: {
           customOption: 'value'
       }
   }
}
{% endhighlight %}

Each context store configuration consists of two parts; a `module` property and a `config`
property.

The `module` property identifies the context store plugin to use. It can either be
the name of built-in module (currently either `memory` or `localfilesystem`), or it should be
a module that has been loaded using `require`.

{% highlight javascript %}
contextStorage: {
   default: {
       module:"memory",
   },
   custom: {
       module:require("my-custom-store")
   }
}
{% endhighlight %}

The `config` property is an object that is passed to the module to provide an
custom options.

### Store Module API

A custom plugin's module must export a single constructor function. This function is called when a
new instance of the plugin is required. The function is passed the value of the `config`
property for the given instance. This allows the runtime to have multiple instances
of the same store plugin, each with its own configuration.

{% highlight javascript %}

var ContextStore = function(config) {
    this.config = config;
}

ContextStore.prototype.open = function() { ... }


module.exports = function(config){
    return new ContextStore(config);
};

{% endhighlight %}


The object returned by the constructor function must implement all of the functions
detailed [here](methods/).
