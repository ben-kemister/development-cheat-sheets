---
title: "Network commands"
tags:
- linux
- hardware
- network
---

Linux commands related to the systems network connection.
<!--more-->

## How to Verify the Speed of the NIC

We can check the speed of our NIC module by using the `/sys` pseudo-filesystem:

```shell
cat /sys/class/net/<interface>/speed
```

The advantage of this method is that we don’t need to install any external software tools.

Before we run the command, we need to know the network interface name. Two easy ways to find it are by using the `ifconfig` command or the `ip` command. 
We can also just list the interfaces within the `/sys/class/net/` directory:

```shell
$ ls /sys/class/net
cni0  enp0s31f6  flannel.1  lo  veth15e576e3  veth409b84fd  vethc1665644  vethf896545a  wlp2s0
```

Then we can run the `cat` command on its speed file. For example, if the interface name is `enp0s31f6` execute:

```shell
$ cat /sys/class/net/enp0s31f6/speed
1000
```

