---
title: Classes
tags:
- javascript
---

This page contains more detailed information about classes in JavaScript.

# Introduction
The `class` keyword was introduced to help developers migrating from other languages. It is *Syntactic Sugar* and doesn't really change the way that the programming language works.

Ultimately classes get turned into JavaScript prototypes eventually.

# Constructor

A `constructor` is a special function that is called immediately after the object is created. It can be used to hold any initialisation logic (such as initialise any properties) for the object before it is used.

{% highlight javascript %}
    
    class MyClass {

        constructor() {
            // Initialise properties
            this._name = 'Default Name';

            // load initial set of data
            this._isInitialised = this._load();
        }
    }
{% endhighlight %}

# Naming Conventions

The name of an object's private properties start with an underscore ( `_` ) for example:

{% highlight javascript %}
    
    class Lady {

        constructor() {
            // naming convention for private properties
            this._age;
        }

        /**
         * A protected/private method.
         * /
        _save() {
            // do something.
        }
    }
{% endhighlight %}

>Note: This does not actually make the property private, it is just a naming convention, and is meant to discourage other developers from changing the value of the property.

# Singletons

You can structure a class to work as a singleton, so that there is only one instance of the class for the entire application.

{% highlight javascript %}
    
    class MySingleton {

        constructor() {
            // initialisation logic
        }

        // Create a 'static' singleton for the entire application
        MySingleton.instance = new MySingleton();

        // Expose the singleton in its own variable
        cost mySingleton = MySingleton.instance;
    }
{% endhighlight %}

