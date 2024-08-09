---
title: "Longhorn"
tags:
- kubernetes
- longhorn
---

[Longhorn](https://longhorn.io/) is a cloud native distributed **block storage** for Kubernetes.
<!--more-->

## Topic Specific Child Pages

{{% children sort="title" description="true" %}}

## Architecture

Below is an overview of longhorn borrowed from the [excellent documentation on the longhorn site](https://longhorn.io/docs/latest/).

![Longhorn overview](./how-longhorn-works.svg)

## Troubleshooting

### "format of disk": mke2fs is apparently in use by system

This can be caused by `multipathd` daemon from adding additional block devices created by Longhorn.

For more details see: 
* [Troubleshooting: `MountVolume.SetUp failed for volume` due to multipathd on the node](https://longhorn.io/kb/troubleshooting-volume-with-multipath/)
* [QUESTION: Mountdevice failed "format of disk": mke2fs is apparently in use by system](https://github.com/longhorn/longhorn/issues/3583)

You can check the block devices with ``lsblk``.

To fix:

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
   
### "Cannot auto-delete Pod when the associated Volume is not found"

These warning messages can be found in the `longhorn-system/longhorn-manager-xxxxx` pod.

```text
time="2024-05-24T20:05:08Z" level=warning msg="Cannot auto-delete Pod when the associated Volume is not found" func="controller.(*KubernetesPodController).getAssociatedVolumes" file="kubernetes_pod_controller.go:466" controller=longhorn-kubernetes-pod error="volume.longhorn.io \"mediawiki-longhorn\" not found" node=machine-1 pod=mediawiki-5dd965df9-66fz2
```

This is caused by a misalignment between the PV name and the (longhorn) volume name, as described [here](https://github.com/longhorn/longhorn/discussions/6419).

To fix, just make sure the name of the PV is the same as the longhorn volume.
