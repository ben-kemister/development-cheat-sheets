---
title: Guacamole
tags:
- guacamole
- remote
- vnc
- rdp
- ssh
---

[Apache Guacamole](https://guacamole.apache.org/) is a clientless remote desktop gateway. It supports standard protocols 
like VNC, RDP, and SSH.
<!--more-->

## Installation

Guacamole can be run natively, from a Docker image and from Kubernetes.

In all cases you need a database for user authentication and storage of configuration. 

## Initial DB setup

```sql
CREATE DATABASE IF NOT EXISTS guacamole_db DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
```