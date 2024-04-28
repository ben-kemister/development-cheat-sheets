---
title: scp
tags:
 - unix
 - linux
 - command
 - copy
---

The `scp` (secure copy) command in Linux system is used to copy file(s) between servers in a secure way.
<!--more-->

## Syntax

```shell
scp [options] [[user@]host1:]source_file_or_directory ... [[user@]host2:]destination
```

For example to copy a file from HostB to HostA while logged into HostA:

```shell
scp username@HostB:/path/to/file /path/to/destination
```