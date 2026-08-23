---
title: "Adding Nodes to a cluster"
date: 2026-08-19
tags:
- k3s
- nodes
- control_plane
- worker_node
---

This page contains information about adding Nodes to an existing k3s cluster.
<!--more-->

## Adding a Worker Node

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


## Adding a Control/Master Node

Before running the command to install the k3s binaries, you need a few pieces of information from one of your K3s control-plane 
(server) nodes:
1. **Server URL**: The IP address of your control-plane node followed by port 6443 (e.g., https://192.168.1.13:6443).
2. **Cluster Token**: This acts as the secret password for the agent to join the cluster. You can find it on your server node by running:
   ```shell
   sudo cat /etc/rancher/k3s/cluster-token
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

Here is the format showing how to join a machine as a control node:
```shell
curl -sfL https://get.k3s.io | K3S_URL=<SERVER_URL> K3S_TOKEN=<NODE_TOKEN> INSTALL_K3S_VERSION=<K3s_VERSION> sh - -s - server
```

For example:
```shell
curl -sfL https://get.k3s.io | K3S_URL=https://192.168.1.13:6443 K3S_TOKEN=K10c<REDACTED>e1c8::server:a1<REDACTED>0c INSTALL_K3S_VERSION=v1.34.8+k3s1 sh - -s - server
```

### Further Configuration

As a control/master node there are a number of other configurations options that you may be using these are detailed below.

#### Cluster Token file

The cluster token can be passed in from a file using the `--token-file /etc/rancher/k3s/cluster-token` argument.

See the systemd service definition `/etc/systemd/system/k3s.service` below for more details.

#### Configuration file

If you have multiple configuration options you want to apply you may find this easier by using a dedicated configuration 
files such as `/etc/rancher/k3s/config.yaml`.

Below is an example configuration file:
```yaml
# /etc/rancher/k3s/config.yaml
---

disable:
   - servicelb
kube-apiserver-arg:
   - admission-control-config-file=/var/lib/rancher/k3s/server/psa.yaml
   - authorization-mode=Node,RBAC
   - oidc-issuer-url=https://dex.example.com
   - oidc-username-claim=email
   - oidc-client-id=k3s-client
protect-kernel-defaults: true
secrets-encryption: true
```

The configuration file can be set using the `--config /etc/rancher/k3s/config.yaml` argument, for example in the systemd 
service definition `/etc/systemd/system/k3s.service`:

```shell
sudo cat /etc/systemd/system/k3s.service
```
```text
[Unit]
Description=Lightweight Kubernetes
Documentation=https://k3s.io
Wants=network-online.target
After=network-online.target

[Service]
Type=notify
ExecStartPre=/bin/sh -xc '! /usr/bin/systemctl is-enabled --quiet nm-cloud-setup.service'
ExecStartPre=-/sbin/modprobe br_netfilter
ExecStartPre=-/sbin/modprobe overlay
ExecStart=/usr/local/bin/k3s server --server https://192.168.1.19:6443 --config /etc/rancher/k3s/config.yaml --token-file /etc/rancher/k3s/cluster-token
KillMode=process
Delegate=yes
LimitNOFILE=1048576
LimitNPROC=infinity
LimitCORE=infinity
TasksMax=infinity
TimeoutStartSec=0
Restart=always
RestartSec=5s

[Install]
WantedBy=multi-user.target
```

#### Pod Security Admission (PSA)

If you are using [Pod Security Admission](./cis_hardening) in your cluster (as per the example configuration 
`/etc/rancher/k3s/config.yaml` above) you will need to create this in `/var/lib/rancher/k3s/server/psa.yaml`.

Below is an example PSA configuration:
```yaml
# /etc/rancher/k3s/config.yaml
#
# This policy should be written to a file named psa.yaml in /var/lib/rancher/k3s/server directory.
#
# Note: If you make changes to this file you will need to restart the k3s service for the changes to be applied
#
# See:
#   - https://docs.k3s.io/security/hardening-guide#pod-security
#   - https://kubernetes.io/docs/concepts/security/pod-security-admission/#pod-security-levels
#   - https://kubernetes.io/docs/concepts/security/pod-security-standards/
#
---
apiVersion: apiserver.config.k8s.io/v1
kind: AdmissionConfiguration
plugins:
   - name: PodSecurity
     configuration:
        apiVersion: pod-security.admission.config.k8s.io/v1beta1
        kind: PodSecurityConfiguration
        defaults:
           # CIS Recommendation: "restricted"
           enforce: "restricted"
           enforce-version: "latest"
           audit: "restricted"
           audit-version: "latest"
           warn: "restricted"
           warn-version: "latest"

        exemptions:
           usernames: []
           runtimeClasses: []
           namespaces:
              # The Kubernetes critical additions such as CNI, DNS, and Ingress are run as pods in the kube-system namespace.
              # Therefore, this namespace will have a policy that is less restrictive so that these components can run properly.
              - kube-system
              - cis-operator-system
```

#### protect-kernel-defaults

If you are hardening your k3s cluster and have set `protect-kernel-defaults: true` (as per the example above you will 
need to create and add a configuration file `/etc/sysctl.d/90-kubelet.conf` to your system to enforce these or else the 
k3s service fails to start.

Create a file called `/etc/sysctl.d/90-kubelet.conf` and add the snippet below:
```shell
sudo nano /etc/sysctl.d/90-kubelet.conf
```
```text
vm.panic_on_oom=0
vm.overcommit_memory=1
kernel.panic=10
kernel.panic_on_oops=1
```

Then run `sysctl -p /etc/sysctl.d/90-kubelet.conf`

For more information see [Ensure protect-kernel-defaults is set](https://docs.k3s.io/security/hardening-guide#ensure-protect-kernel-defaults-is-set)



