---
title: Imports
tags:
- javascript
---

This page contains information, and simple code examples, about the use of the JavaScript imports.

# Introducing Imports

JavaScript Imports come in several flavours (read syntaxes) that have been introduced as the language has evolved (ES5 -> ES6 etc.). The different syntaxes also provide different functionality.

## ES6 Imports

In ES6 the import syntax is a declarative import syntax, it does not execute any functions.
So if you want to call a function you'll need two lines:

{% highlight javascript %}

    import debugModule from 'debug';
    const debug = debugModule('http');
{% endhighlight %}
