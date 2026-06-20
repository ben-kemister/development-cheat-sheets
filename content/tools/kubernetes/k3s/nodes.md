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


## Adding a machine as a Node

### Adding a Worker Node

To install additional agent nodes and add them to the cluster, run the installation script with the `K3S_URL`, `K3S_TOKEN`,
and optionally `INSTALL_K3S_VERSION`, environment variables. 

Before running the command, you need a few pieces of information from your K3s control-plane (server) node:
1. **Server URL**: The IP address of your control-plane node followed by port 6443 (e.g., https://192.168.1.100:6443).
2. **Node Token**: This acts as the secret password for the agent to join the cluster. You can find it on your server node by running:
   ```shell
   sudo cat /var/lib/rancher/k3s/server/node-token
   ```
3. (Optional) ***K3s Version*: If you are not running the latest version of k3s you will need to find out what version you are currenlty running:
   ```shell
   kubectl version
   ```
   The example response below show the cluster is running `v1.34.8+k3s1`
   ```text
   ...
   Server Version: v1.34.8+k3s1
   ```

Here is the format showing how to join a machine as a worker node:
```shell
curl -sfL https://get.k3s.io | K3S_URL=<SERVER_URL> K3S_TOKEN=<NODE_TOKEN> INSTALL_K3S_VERSION=<K3s_VERSION> sh -
```

For example:
```shell
curl -sfL https://get.k3s.io | K3S_URL=https://192.168.1.100:6443 K3S_TOKEN=K10c<REDACTED>e1c8::server:a1<REDACTED>0c INSTALL_K3S_VERSION=v1.34.8+k3s1 sh -
```
```text
[INFO]  Using v1.34.8+k3s1 as release
[INFO]  Downloading hash https://github.com/k3s-io/k3s/releases/download/v1.34.8%2Bk3s1/sha256sum-amd64.txt
[INFO]  Downloading binary https://github.com/k3s-io/k3s/releases/download/v1.34.8%2Bk3s1/k3s
[INFO]  Verifying binary download
[INFO]  Installing k3s to /usr/local/bin/k3s
[INFO]  Skipping installation of SELinux RPM
[INFO]  Creating /usr/local/bin/kubectl symlink to k3s
[INFO]  Creating /usr/local/bin/crictl symlink to k3s
[INFO]  Creating /usr/local/bin/ctr symlink to k3s
[INFO]  Creating killall script /usr/local/bin/k3s-killall.sh
[INFO]  Creating uninstall script /usr/local/bin/k3s-agent-uninstall.sh
[INFO]  env: Creating environment file /etc/systemd/system/k3s-agent.service.env
[INFO]  systemd: Creating service file /etc/systemd/system/k3s-agent.service
[INFO]  systemd: Enabling k3s-agent unit
Created symlink '/etc/systemd/system/multi-user.target.wants/k3s-agent.service' → '/etc/systemd/system/k3s-agent.service'.
[INFO]  systemd: Starting k3s-agent
```

