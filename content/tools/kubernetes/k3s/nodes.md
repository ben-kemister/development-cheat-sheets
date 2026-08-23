---
title: "Nodes"
date: 2026-06-20
tags:
  - k3s
  - nodes
---

This page contains information about the k3s Nodes.
<!--more-->

## Machine Configuration Considerations

This section provides an outline about some of the configuration considerations for the machines that are used as the k3s 
clusters' Nodes.


### DNS Resolution

If you plan to run a DNS server within the k3s cluster, it is a good idea to make sure that the machines are configured 
to use a secondary (or even tertiary) DNS server, if/when the DNS server in the cluster is unavailable.     
See the [Linux DNS configuration](../../../operating_systems/linux/network/dns) page for instructions.


### Private Image Registry

If you are using a private image registry with proxy registries you may need to create a `/etc/rancher/k3s/registries.yaml`
so the node pulls image from your private image registry.   
For more information see the [k3s Private Registry](./private_registry) page.

1. Create the `/etc/rancher/k3s/` directory:
    ```shell
    sudo mkdir -p /etc/rancher/k3s/
    ```
2. Create the `registries.yaml` file:
    ```shell
    sudo nano /etc/rancher/k3s/registries.yaml
    ```
3. In order for the registry changes to take effect, you need to restart K3s on the node.
    ```shell
    # For Master Nodes:
    sudo systemctl restart k3s
    # For Worker Nodes:
    sudo systemctl restart k3s-agent
    ```
4. Done!


### Longhorn - multipathd configuration

If you are using [Longhorn](../longhorn/_index) on your cluster you will need to configure `multipathd` to avoid getting 
`MountVolume.SetUp failed for volume` errors. For more information see: 
[Troubleshooting: `MountVolume.SetUp failed for volume` due to multipathd on the node](https://longhorn.io/kb/troubleshooting-volume-with-multipath/)

1. Create/edit `/etc/multipath.conf`
    ```shell
    sudo nano /etc/multipath.conf
    ```
2. Add the following to `/etc/multipath.conf` file:
    ```text
    blacklist {
       devnode "^sd[a-z0-9]+"
    }
    ```
3. Restart the `multipathd` service:
    ```shell
    sudo systemctl restart multipathd
    ```
4. Done!

### Mounting NFS drives

If you want to mount NFS drives the machine will need the `nfs-common` packages installed:

```shell
sudo apt update && sudo apt install -y nfs-common
```

### Remove password prompt for `sudo`

If you intend on using automated operating system updates you will need to remove the password prompt for ``sudo``:

Launch `visudo`:
```shell
sudo visudo
```

Add a line using the following syntax `<USERNAME> ALL=(ALL) NOPASSWD: ALL`, for example for the username 'smith':
```text
smith   ALL=(ALL) NOPASSWD: ALL
```

For more information see: [visudo](../../../operating_systems/linux/sudo/visudo)


