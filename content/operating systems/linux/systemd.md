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

## Handy Commands

| Command                           | Description                                          |
|-----------------------------------|------------------------------------------------------|
| `systemctl status <SERVICE_NAME>` | Displays the status of the service                   | 
| `systemctl start <SERVICE_NAME>`  | Starts the service                                   | 
| `systemctl enable <SERVICE_NAME>` | Enables the service to be started on boo             |
| `sudo systemctl daemon-reload`    | Reload the `Systemd` daemon to detect new unit files |


## Creating a service

Systemd uses `*.service` files, known as _unit_ files located in the `/etc/systemd/system/` directory.

> TODO details of how to create a service/unit file....


