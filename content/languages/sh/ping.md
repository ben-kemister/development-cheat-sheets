---
title: "ping"
tags:
- sh
- bash
- linux
- terminal
- network
---

Information about the use of the `ping` command in linux.
<!--more-->

## Ping all IP Addresses in range

You can use the simple script below to ping all the IP addresses in a range, then print those which gave a response:

```shell
alive=('Pings returned from:'); \
  for i in {1..255}; do IP_ADD=192.168.0.$i; ping -q -w 1 $IP_ADD && alive+=($IP_ADD); done; \
  echo ""; printf '%s\n' "${alive[@]}"
```