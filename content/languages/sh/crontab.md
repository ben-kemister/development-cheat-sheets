---
title: crontab
tags:
 - unix
 - linux
---

The [crontab](https://man7.org/linux/man-pages/man5/crontab.5.html) command in Linux is used to manage scheduled tasks (cron jobs). 
<!--more-->
It allows users to create, edit, list, and remove cron jobs, effectively automating tasks at specific times or intervals.

## List cron jobs

Use the `crontab -l` command to list/view the current cron jobs, for example:

```shell
crontab -l
```
```text
59 1  * * 0 PATH="$PATH:/usr/sbin:/usr/local/bin/" pihole updateGravity >/var/log/pihole/pihole_updateGravity.log || cat /var/log/pihole/pihole_updateGravity.log
00 00 * * * PATH="$PATH:/usr/sbin:/usr/local/bin/" pihole flush once quiet
59 17 * * * PATH="$PATH:/usr/sbin:/usr/local/bin/" pihole updatechecker
```
