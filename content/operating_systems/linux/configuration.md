---
title: Configuration (Linux) 
tags:
- linux
- configuration
- bash
- sh
---

This page provides information about how to configure linux.
<!--more-->

## Change the hostname

For more information on changing the hostname see [this webpage](https://phoenixnap.com/kb/linux-hostname-command).

### Temporary change

In the terminal, type the following, replacing *new-hostname* with the name you choose:

```sh
sudo hostname new-hostname
```

### Permanent change

Use `set-hostname` to Change the Hostname, type the following command:

```sh
hostnamectl set-hostname new-hostname
```
Use your own hostname choice instead of *new-hostname*.

You can confirm the change using `hostnamectl` and checking the output.

