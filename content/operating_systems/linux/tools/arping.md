---
title: arping
tags:
 - unix
 - linux
 - network
 - arp
---

[arping](https://man7.org/linux/man-pages/man8/arping.8.html) is a command-line utility, similar to ping, 
but it uses the Address Resolution Protocol (ARP) to probe hosts on a local network. 
<!--more-->
It's used to discover which MAC addresses are associated with specific IP addresses on a local network segment. 
Essentially, it sends ARP requests to determine if a host at a given IP address is reachable and responsive.

## What is ARP?

ARP stands for Address Resolution Protocol. 
It's a protocol used in computer networking to map IP addresses (Layer 3) to MAC addresses (Layer 2) within a local network.

This mapping is crucial for devices to communicate on the same network, as devices need to know each other's MAC addresses to send data packets directly.

## Basic usage

The basic syntax is `arping [options] <IP_ADDRESS>`, for example:

```shell
arping 192.39.59.17
```
```text
ARPING 192.39.59.17 from 192.39.59.16 eth0
Unicast reply from 192.39.59.17 [00:50:56:B2:AB:CD]  0.754ms
Unicast reply from 192.39.59.17 [00:50:56:B2:AB:CD]  0.668ms
Unicast reply from 192.39.59.17 [00:50:56:B2:AB:CD]  0.676ms
Unicast reply from 192.39.59.17 [00:50:56:B2:AB:CD]  0.672ms
Unicast reply from 192.39.59.17 [00:50:56:B2:AB:CD]  0.663ms
^CSent 5 probes (1 broadcast(s))
Received 5 response(s)
```




