---
title: Ports
tags:
- linux
- ports
---


Information about ports in linux
<!--more-->


## Test if a remote port is open

There are multiple ways to check if a port is open on a remote system with linux.    

When you have limited tools available you can use:
```shell
echo > /dev/tcp/[host]/[port] && echo "Port is open"
```

For example:
```shell
echo > /dev/tcp/192.168.0.1/8080 && echo "Port is open"
#Port is open
```
