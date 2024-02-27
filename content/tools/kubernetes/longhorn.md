---
title: "Longhorn"
tags:
- kubernetes
- longhorn
---

[Longhorn](https://longhorn.io/) is a cloud native distributed block storage for Kubernetes.
<!--more-->


## Moving files into Longhorn volume

You can use the following Job manifest to copy files from the old PVC to the new one:

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
