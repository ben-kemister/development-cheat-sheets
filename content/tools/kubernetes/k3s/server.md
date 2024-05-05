---
title: "Server"
date: 2024-02-04T05:38:31+11:00
tags:
  - k3s
  - server
---

This page contains information about the k3s server.
<!--more-->

## Important Directories and paths

| Name        | Path                           | Notes                             |
|-------------|--------------------------------|-----------------------------------|
| Server root | `/var/lib/rancher/k3s/server/` | Root for all server related files |
| `systemd` service | `/etc/systemd/system/k3s.service` | |
| Node configuration | `/etc/rancher/k3s/config.yaml` |                                                             |


## Changing the k3s service

When installed as a service, the service file is located in: `/etc/systemd/system/k3s.service`

You can alter the `ExecStart=` entry if you need to change the startup arguments.

Just remember to reload the systemctl daemon and restart the service so that the settings are applied:

```shell
sudo systemctl daemon-reload
sudo systemctl restart k3s
# Check the status
sudo systemctl status k3s
```

## Secrets Encryption

k3s can be [configured to encrypt Secrets](https://docs.k3s.io/security/secrets-encryption) at rest (i.e. within the etcd database).

When enabled server nodes will have an encryption config file: `/var/lib/rancher/k3s/server/cred/encryption-config.json`

k3s has a [command tool](https://docs.k3s.io/cli/secrets-encrypt) for dealing with secrets-encryption, 
on the main server node `sudo k3s secrets-encrypt --help`.

## Certificates

By default, certificates in K3s expire in 12 months.
If the certificates are expired or have fewer than 90 days remaining before they expire, the certificates are rotated when K3s is restarted.

    Note: If the certificates have expired you will not be able to access the cluster with `kubeclt`

For more see [Certificate Rotation](https://docs.k3s.io/advanced#certificate-rotation).

## Changing IP address of the server

On the worker nodes edit the `sudo nano /etc/systemd/system/k3s-agent.service.env` file to change the IP address of the server.