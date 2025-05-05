---
title: tcpdump
tags:
 - unix
 - linux
 - network
---

[TCPdump](https://www.tcpdump.org/) is a command-line packet analyzer used to capture and display network traffic in real-time, 
allowing users to examine individual data packets and analyze communication between devices. 
<!--more-->
It's a versatile tool often used for network troubleshooting, security monitoring, and analyzing network performance.

## List available interfaces

To begin, use the command `tcpdump --list-interfaces` (or `-D` for short) to see which interfaces are available for capture:

```shell
sudo tcpdump -D
```
```text
1.enp0s31f6 [Up, Running, Connected]
...
51.any (Pseudo-device that captures on all interfaces) [Up, Running]
52.lo [Up, Running, Loopback]
53.bluetooth-monitor (Bluetooth Linux Monitor) [Wireless]
...
```

## Basic usage

The basic syntax is `sudo tcpdump <--interface|-i> <INTERFACE> [options] [FILTER]`, for example:

### Options

* `-n` - disable name resolution
* `-nn` - disable port resolution





