---
title: Filesystem Hierarchy
tags:
- linux
- files
- file_system
---

The [Filesystem Hierarchy Standard (FHS)](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard) is a reference describing the conventions used for the layout of Unix-like systems.
<!--more-->
It is maintained by the Linux Foundation. The latest version is [3.0](https://refspecs.linuxfoundation.org/fhs.shtml), released on 3 June 2015.

## Simplified Linux File System Hierarchy

![Linux File System Hierarchy](simplified-linux-file-system-hierarchy.jpg)

## Handy File Paths

| Path       | Description                           |
|------------|---------------------------------------|
| `/var` | Variable files: files whose content is expected to continually change during normal operation of the system, such as logs, spool files, and temporary e-mail files. |
| `/var/opt` | Variable data from add-on packages that are stored in `/opt` |

## References & Links

* [Filesystem Hierarchy Standard - Wikipedia](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard)