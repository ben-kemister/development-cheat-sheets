---
title: run
tags:
- podman
- image
- container
---

This page contains examples about the use of Podman's `run` command (i.e. to run a container).
<!--more-->

## Environmental variables

You can set environmental variables in your container by using the syntax: `-e "VARIABLE_NAME=value"`.

Just add this argument to your `run` command before you specify the image. For example:

```shell
podamn run --rm -e "MY_VAR=Hello" alpine:latest env      
```
Returns:
```text
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=900fab963e1f
TERM=xterm
MY_VAR=Hello
HOME=/root
```

### Exposing Ports

To expose ports when running a container use the syntax: `-p <HOST_PORT>:<CONTAINER:PORT>`

For example:
```shell
podman run -p 3000:3000 my_next_js_app
```

> To expose UDP ports add the `/udp` suffix to the port mapping.    
> `podman run -p 80:80/tcp -p 80:80/udp my_app`


