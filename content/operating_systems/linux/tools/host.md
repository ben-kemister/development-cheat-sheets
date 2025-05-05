---
title: host
tags:
 - unix
 - linux
 - dns
 - network
---

[host](https://linux.die.net/man/1/host) is a linux DNS lookup utility. 
<!--more-->
It is normally used to convert names to IP addresses and vice versa.

## Basic usage

The basic syntax is `host <NAME_TO_RESOLVE>`, for example:

```shell
$ host my-host.subdomain.dns.org
my-host.subdomain.duckdns.org is an alias for subdomain.duckdns.org.
subdomain.duckdns.org is an alias for my-machine_name.
my-machine_name has address 192.168.0.XX
```

## Using a specific DNS server

```shell
$ host my-host.subdomain.duckdns.org 192.168.0.1
Using domain server:
Name: 192.168.0.1
Address: 192.168.0.1#53
Aliases:

my-host.subdomain.duckdns.org is an alias for subdomain.duckdns.org.
subdomain.duckdns.org is an alias for my-machine_name.
my-machine_name has address 192.168.0.XX
```




