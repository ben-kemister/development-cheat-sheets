---
title: netcat (nc)
tags:
- linux
- port
- network
---

The [nc](https://linux.die.net/man/1/nc) (or netcat) utility is used for just about anything under the sun involving TCP or UDP. 
<!--more-->
It can open TCP connections, send UDP packets, listen on arbitrary TCP and UDP ports, do port scanning, and deal with both IPv4 and IPv6. 
Unlike telnet, `nc` scripts nicely, and separates error messages onto standard error instead of sending them to standard output, as telnet does with some.

## General Syntax

``netcat [options] host port``

Common Options:
* `-v` - verbose mode
* `-u` - use a UDP connection
* `-l` - listen mode (rather than connect)

## Listen on a Port

```shell
netcat -l <PORT>
```

## Connection to a remote machine

```shell
netcat <REMOTE_HOST> <PORT>
```

## Simple Web Server

```html
<!--
Filename: index.html
-->
<html>
        <head>
                <title>Test Page</title>
        </head>
        <body>
                <h1>Level 1 header</h1>
                <h2>Subheading</h2>
                <p>Normal text here</p>
        </body>
</html>
```

```shell
printf 'HTTP/1.1 200 OK\n\n%s' "$(cat index.html)" | netcat -l 8888
```


