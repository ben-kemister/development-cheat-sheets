---
title: Javascript UI Components
---

This page contains some information about common, or handy, UI (browser) components in Javascript.

# Confirmation Window

The confirmation window is available in every browser and will create a simple popup dialog requesting the user to select a button.

{% highlight javascript %}
    var answer = window.confirm("Click OK, get true. Click CANCEL, get false.");

{% endhighlight %}

# Prompt Window

The prompt window allows shows a popup dialog which can capture a string input from the user.

{% highlight javascript %}
    var answer = window.prompt("Type YES, NO or MAYBE. Then click OK.");

{% endhighlight %}

# The `document` object

The `document` object is the interface that the browser provides so that you can interact with what is on the page.

## document properties

The `document` object has the following handy properties:
* title - the title of the page
