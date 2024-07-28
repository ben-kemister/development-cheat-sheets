---
title: systemd
tags:
- operating_system
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

## Handy Commands

| Command                               | Description                                          |
|---------------------------------------|------------------------------------------------------|
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


