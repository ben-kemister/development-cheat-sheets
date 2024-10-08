---
title: rsync
tags:
 - unix
 - linux
 - files
---

[rsync](https://linux.die.net/man/1/rsync) is a fast, versatile, remote (and local) file-copying tool for linux.
<!--more-->

## Flags

rsync has **heaps** of flags. Below are some of the ones that I have needed for my work:

* `-a` = `-rlptgoD`
* `-p` = Set permissions on destination files
* `-g` = Set group on destination
* `-o` = Preserve owner

## Examples

### Single file with progress

```shell
rsync --progress /path/to/source-file /path/to/destination
source-file
    264,601,600  25%  126.22MB/s    0:00:06
```

### Ignore Existing

You can use the `--ignore-existing` flag to skip updating files that exist on receiver.

### Copy to mapped NFS drive

```shell
# Flags picked to avoid any 'Operations Not Permitted' errors
rsync -rltDvz --del --force --ignore-errors /source/ /backup/
```

## Links

* [16 Rsync Command Examples for Efficient File Synchronization](https://www.tecmint.com/rsync-local-remote-file-synchronization-commands/#2_CopySync_Directory_Locally)
* [Running rsync as root: Operations Not Permitted](https://stackoverflow.com/questions/30671292/running-rsync-as-root-operations-not-permitted)