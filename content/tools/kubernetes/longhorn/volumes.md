---
title: "Volumes"
tags:
- longhorn
- volumes
---

A Longhorn [volume](https://longhorn.io/docs/1.7.0/terminology/#longhorn-volume) is a Kubernetes Custom Resource Definition (CRD) which
describes a Kubernetes volume that is replicated and managed by the Longhorn Manager.
<!--more-->
For each volume, the Longhorn Manager also creates:
* An instance of the Longhorn Engine
* Replicas of the volume, where each replica consists of a series of snapshots of the volume

* Each replica contains a chain of snapshots, which record the changes in the volume’s history. 
* Three replicas are created by default, and they are usually stored on separate nodes for high availability.

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

## Specifying a Node or Disk

You can use [storage tags](https://longhorn.io/docs/1.6.2/nodes-and-volumes/nodes/storage-tags/) to request Longhorn to 
use particular nodes or disks.

This is useful if you want to host a volume on special class of disk like `ssd` or `nvme`.

On the volume side you can specify this with either:
* parameters on a StorageClass, or
* within the Volume spec

### Volume specification

```yaml
apiVersion: longhorn.io/v1beta2
kind: Volume
metadata:
  name: mealie-v2
  namespace: longhorn-system
  labels:
    recurring-job-group.longhorn.io/daily_backup_group: enabled
  annotations:
    # Prevent ArgoCD from automatically deleting this volume
    argocd.argoproj.io/sync-options: Delete=false
spec:
  accessMode: rwo
  dataLocality: best-effort
  encrypted: false
  frontend: blockdev
  numberOfReplicas: 1
  size: "524288000" # 5OO Mi
  snapshotMaxCount: 5
  staleReplicaTimeout: 20
  diskSelector:
    # Run this volume from a SSD to improve UI responsiveness
    - ssd
```

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