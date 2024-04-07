---
title: "Longhorn"
tags:
- kubernetes
- longhorn
---

[Longhorn](https://longhorn.io/) is a cloud native distributed **block storage** for Kubernetes.
<!--more-->

## Architecture

Below is an overview of longhorn borrowed from the [excellent documentation on the longhorn site](https://longhorn.io/docs/latest/).

![Longhorn overview](./how-longhorn-works.svg)

## Moving files into Longhorn volume

You can use the following [Job manifest](https://github.com/longhorn/longhorn/blob/master/examples/data_migration.yaml#L29-L31) 
to copy files from the old PVC to the new one:

```yaml
#
# Description: Simple Job which copies the files from one PVC to another
#
#     Based on: https://github.com/longhorn/longhorn/blob/master/examples/data_migration.yaml#L29-L31
#
# To apply/run:
#   kubectl apply -f ./volume-migration.yaml
#
---
apiVersion: batch/v1
kind: Job
metadata:
  # namespace where the pvc's exist
  namespace: default
  name: volume-migration
spec:
  completions: 1
  parallelism: 1
  backoffLimit: 3
  template:
    metadata:
      name: volume-migration
      labels:
        name: volume-migration
    spec:
      restartPolicy: Never
      containers:
        - name: volume-migration
#          image: ubuntu:xenial
          image: ubuntu:24.04
          tty: true
          command: [ "/bin/sh" ]
#          args: [ "-c", "while true; do date; sleep 2; done" ]
          args: [ "-c", "cp -r -v /mnt/old/. /mnt/new" ]
          volumeMounts:
            - name: old-vol
              mountPath: /mnt/old
            - name: new-vol
              mountPath: /mnt/new
      volumes:
        - name: old-vol
          persistentVolumeClaim:
            claimName: data-source-pvc # change to data source pvc
        - name: new-vol
          persistentVolumeClaim:
            claimName: data-target-pvc # change to data target pvc
```

## Manually create volume

You can create longhorn volumes using a manifest like:

```yaml
#
# kubectl apply -f .\longhorn-volumes.yaml
#
---
apiVersion: longhorn.io/v1beta2
kind: Volume
metadata:
  name: harbor-registry
  namespace: longhorn-system
  annotations:
    # Prevent ArgoCD from automatically deleting this resource
    argocd.argoproj.io/sync-options: Delete=false
spec:
  accessMode: rwo
  dataLocality: best-effort
  encrypted: false
  frontend: blockdev
  numberOfReplicas: 1
  size: "5368709120" # 5 Gi
  snapshotMaxCount: 5
  staleReplicaTimeout: 30
```

You can then create a PV and PVC to use this volume:

```yaml
#
# kubectl apply -f ./harbor-registry-volume.yaml
#
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: harbor-registry
spec:
  accessModes:
    - ReadWriteOnce
  capacity:
    storage: 5Gi
  csi:
    driver: driver.longhorn.io
    fsType: ext4
    volumeHandle: harbor-registry
  persistentVolumeReclaimPolicy: Retain
  storageClassName: longhorn-persistent
  volumeMode: Filesystem
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: harbor-registry
  namespace: default
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: longhorn-persistent
  resources:
    requests:
      storage: 5Gi
  volumeName: harbor-registry
```

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