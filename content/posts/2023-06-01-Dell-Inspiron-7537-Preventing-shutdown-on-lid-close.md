---
title: "Dell Inspiron 7537 - Preventing Linux shutdown when closing the lid"
draft: false
tags:
 - dell
 - Inspiron
 - k3s
 - kubernetes
 - linux
 - mint
---

This is a little post about how to prevent a Dell Inspiron laptop from shutting down or going into standby
when you close the lid on the laptop.
<!--more-->
This issue came up when I was trying to repurpose an old Dell Inspiron 7537 laptop as a Kubernetes (k3s) worker node.

For some reason I couldn't get Ubuntu server to install on the laptop so instead I just installed Linux Mint which
has worked really well on that laptop in the past

## It's all about `/etc/systemd/logind.conf`

After discovering that changing the settings in the Mint GUI didn't seem to work I came across 
[this post on stackoverflow](https://askubuntu.com/questions/15520/how-can-i-tell-ubuntu-to-do-nothing-when-i-close-my-laptop-lid).

For me this meant changing ``/etc/systemd/logind.conf`` as follows:

```shell
kem@dell-inspiron-7537:~$ sudo nano /etc/systemd/logind.conf

...
# Old default value
#HandleLidSwitch=suspend
HandleLidSwitch=ignore
...
# Old default value
#HandleLidSwitchExternalPower=suspend
HandleLidSwitchExternalPower=ignore
...
```
Then:
```shell
sudo systemctl restart systemd-logind

sudo reboot
```

This looks to have worked for my laptop. Reading further it looks like you can also add drop
"drop-ins" in the `logind.conf.d/` subdirectory.

> Defaults can be restored by simply deleting this file and all drop-ins.
