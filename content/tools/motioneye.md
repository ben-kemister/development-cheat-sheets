---
title: "motionEye"
date: "2026-08-13"
tags: [motionEye, motion, surveillance, video-monitoring, web-interface]
---

[motionEye](https://github.com/motioneye-project/motioneye) is a web-based graphical user interface for [motion](https://motion-project.github.io/index.html), 
a command-line video surveillance program. 
<!--more-->

## Overview

[motionEye](https://github.com/motioneye-project/motioneye) provides a management layer for the `motion` daemon, 
enabling users to monitor video feeds and manage camera settings through a web browser. 
It is designed to transform devices like a Raspberry Pi into a centralized home security system.

*   **motion (Backend):** A headless, command-line program that monitors video signals, detects movement, and triggers actions (saving files, invoking scripts).
*   **motionEye (Frontend):** An online interface that provides a dashboard for managing multiple cameras, adjusting motion sensitivity, and viewing recordings.

## Key Features

*   **Multi-camera Management:** Centralized dashboard for managing several cameras from different brands.
*   **Motion Detection:** Configurable sensitivity and event-based triggers via the `motion` backend.
*   **Storage & Cloud Integration:** Saves captured media locally or syncs to cloud services like Google Drive.
*   **Live Streaming:** Real-time video passthrough from IP cameras or local webcams.
*   **Extensibility:** Support for custom scripts and database logging for activity.

## Installing motionEye

> TODO...

## Configuration

### motion

`/etc/motioneye/motion.conf`

### motionEyee

`/etc/motioneye/motioneye.conf`

## Running motionEye

### systemd service

```shell
$ cat /etc/systemd/system/motioneye.service
```
```text
[Unit]
Description=motionEye Server

[Service]
ExecStart=/usr/local/bin/meyectl startserver -c /etc/motioneye/motioneye.conf
Restart=on-abort

[Install]
WantedBy=multi-user.target
```

### Handy Links
*   [motionEye GitHub Repository](https://github.com/motioneye-project/motioneye)
*   [motion Project Home](https://motion-project.github.io/index.html)
*   [motionEye Wiki](https://github.com/motioneye-project/motioneye/wiki)

