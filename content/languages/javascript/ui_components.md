---
title: UI Components
tags:
- javascript
- user_interface
---

This page contains some information about common, or handy, UI (browser) components in Javascript.

# The window Object

There is a global `window` object available when working in a browser, all global variables are properties of the `window` object.

## Confirmation Window

The confirmation window is available in every browser and will create a simple popup dialog requesting the user to select a button.

```javascript
var answer = window.confirm("Click OK, get true. Click CANCEL, get false.");
```

## Prompt Window

The prompt window allows shows a popup dialog which can capture a string input from the user.

```javascript
var answer = window.prompt("Type YES, NO or MAYBE. Then click OK.");
```

## Redirect (replace())

The `replace()` method of the Location interface replaces the current resource with the one at the provided URL. 
The difference from the `assign()` method is that after using replace() the current page will not be saved in session History, 
meaning the user won't be able to use the back button to navigate to it.

You can use the `window.location.replace($url);` function to act like a redirect in javascript.

For example:

```javascript
// similar behavior as an HTTP redirect
window.location.replace("http://stackoverflow.com");
```

## setTimeout() method

The `setTimeout()` method of the WindowOrWorkerGlobalScope mixin (and successor to `Window.setTimeout()`) sets a timer 
which executes a function or specified piece of code once the timer expires.

```javascript
var timeoutID;

function delayedAlert() {
    timeoutID = window.setTimeout(window.alert, 2*1000, 'That was really slow!');
}

function clearAlert() {
    window.clearTimeout(timeoutID);
}
```

# The *document* object

The `document` object is the interface that the browser provides so that you can interact with what is on the page.

## document properties

The `document` object has the following handy properties:
* title - the title of the page
