---
title: Red Hat CoreOS (RHCOS)
date: 2025-04-03T09:58:18+11:00
tags:
  - operating_system
  - linux
  - red_hat
---

Red Hat Enterprise Linux CoreOS (RHCOS) is a container-oriented operating system, 
<!--more-->
designed as a component of [OpenShift Container Platform](../tools/kubernetes/openshift), providing a focused, immutable, 
and secure foundation for running containerized workloads.

## Starting Red Hat CoreOS (RHCOS) in emergency mode

Typically, you would SSH into CoreOS to perform any diagnostics, however this will not work if there is a network miss-configuration.
In that case you can boot the machine into _single user_ mode where you can get a console as root.

The process is as follows:
1. Restart the machine
2. Intercept the GRUB menu with `e` to edit the GRUB entry
3. Modify the kernel command line:
   1. Remove the console entry `console=ttySo,1115200n8`
   2. Add `single`
4. Press `Ctrl + X` to resume booting with the modified GRUB entry

This process was found here: [How to start Red Hat CoreOS (RHCOS) in emergency mode in OpenShift Container Platform 4](https://access.redhat.com/solutions/5500131)


