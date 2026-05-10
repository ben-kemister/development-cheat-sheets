---
title: systemd
tags:
- linux
- services
---

[Systemd](https://systemd.io/) is a suite of basic building blocks for a Linux system. 
It provides a system and service manager that runs as PID 1 and starts the rest of the system.
<!--more-->
Of particular interest to users is its use as an init system used to bootstrap user space and manage user processes.

## Logging

`journalctl` is a utility for querying and displaying logs from `journald`, `systemd`'s logging service.

You can use `journalctl` to view (tail) the logs with:
```shell
# Tail all logs
journalctl -f

# Tail the logs for the k3s service
sudo journalctl -u k3s -f
``` 

For more examples see: [How to Use the Journalctl Command to Tail Service Logs in Linux](https://linuxier.com/how-to-use-journalctl-command-to-tail-service-logs/)

## Show failed units

To show all failed units using `systemctl`, the most direct command is: `systemctl --failed`.

This is a built-in shortcut for `systemctl list-units --state=failed`. 
It provides a summary of all units (services, sockets, timers, etc.) that have entered a failed state.

```shell
$ sudo systemctl --failed
  UNIT                        LOAD   ACTIVE SUB    DESCRIPTION
● irqbalance.service          loaded failed failed irqbalance daemon
● iscsid.service              loaded failed failed iSCSI initiator daemon (iscsid)
● networkd-dispatcher.service loaded failed failed Dispatcher daemon for systemd-networkd
● packagekit.service          loaded failed failed PackageKit Daemon
● rpc-statd.service           loaded failed failed NFS status monitor for NFSv2/3 locking.
● unattended-upgrades.service loaded failed failed Unattended Upgrades Shutdown

LOAD   = Reflects whether the unit definition was properly loaded.
ACTIVE = The high-level unit activation state, i.e. generalization of SUB.
SUB    = The low-level unit activation state, values depend on unit type.
```

### Tracing failed dependencies

The tree view printed out by `systemctl list-dependencies <SERVICE_NAME>` to help trace any failed dependencies. 

```shell
$ sudo systemctl list-dependencies networkd-dispatcher.service
networkd-dispatcher.service
● ├─system.slice
● └─sysinit.target
●   ├─apparmor.service
●   ├─blk-availability.service
●   ├─dev-hugepages.mount
●   ├─dev-mqueue.mount
●   ├─finalrd.service
×   ├─iscsid.service     <-- FAILED DEPENDENCY!
●   ├─keyboard-setup.service
...
```

## Handy Commands

| Command                               | Description                                          |
|---------------------------------------|------------------------------------------------------|
| `systemctl list-units --type=service` | List all available (service) units                   |
| `systemctl list-units --type=service` | List all available (service) units                   |
| `systemctl status <SERVICE_NAME>`     | Displays the status of the service                   | 
| `systemctl start <SERVICE_NAME>`      | Starts the service                                   | 
| `systemctl stop <SERVICE_NAME>`       | Stops the service                                    | 
| `systemctl restart <SERVICE_NAME>`    | Restarts the service                                 | 
| `systemctl enable <SERVICE_NAME>`     | Enables the service to be started on boo             |
| `sudo systemctl daemon-reload`        | Reload the `Systemd` daemon to detect new unit files |
| `sudo journalctl -u k3s -f`           | Follow the logs for the `k3s` service                |


## Creating a service

Systemd uses `*.service` files, known as _unit_ files located in the `/etc/systemd/system/` directory.

> TODO details of how to create a service/unit file....


