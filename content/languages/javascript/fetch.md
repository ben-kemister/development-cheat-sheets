---
title: Fetch (API)
tags:
- javascript
- fetch
---

The [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) provides a JavaScript interface for accessing and manipulating parts of the protocol, such as requests and responses. 
It also provides a global `fetch()` method that provides an easy, logical way to fetch resources asynchronously across the network.
<!--more-->
Unlike `XMLHttpRequest` that is a callback-based API, Fetch is **promise-based** and provides a better alternative that can be easily used in service workers. 
Fetch also integrates advanced HTTP concepts such as **CORS** and other extensions to **HTTP**.

## Overview

When you use the `fetch()` method to make a request to a server, you will get back a promise that resolves to a Response object. 
The Response object represents the entire HTTP response, including the status, headers, and body. 
However, the body is not directly accessible as a string or an object, but rather as a stream of data that can only be read once.

## How to get response body

To handle the response body, you need to use one of the methods of the Response object that returns another promise that resolves to the parsed data. 
These methods are:

* `.json()`: Returns a Promise that resolves with the parsed JSON.
* `.text()`: Returns a promise that resolves with a String.
* `.blob()`: Returns a promise that resolves with a Blob, which represents a file-like object of immutable, raw data.
* `.arrayBuffer()`: Returns a promise that resolves with an ArrayBuffer, which represents a generic, fixed-length binary data buffer.
* `.formData()`: Returns a promise that resolves with a FormData, which is a key-value pair data structure used to submit form data.

For more information see: [How to get response body when using fetch() in JavaScript](https://byby.dev/js-fetch-get-response-body).

### Body as JSON

Example:

```javascript
// Call a server side PHP script
fetch("../php/checkUrl.php?url=" + url)
    .then(response => response.json())
    .then( data => {
        console.log(`Returned: '${data}'`);
    })
```