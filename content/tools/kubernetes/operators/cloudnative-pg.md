---
title: CloudNativePG
tags:
- operators
- postgresql 
---

[CloudNativePG](https://cloudnative-pg.io/) is the Kubernetes operator that covers the full lifecycle of a highly 
available PostgreSQL database cluster with a primary/standby architecture, using native streaming replication.
<!--more-->

## How CloudNativePG Works

The operator continuously monitors and updates the PostgreSQL cluster state. Examples of automated actions include:

* Failover management: If the primary instance fails, the operator elects a new primary, updates the cluster status, and orchestrates the transition.
* Scaling read replicas: When the number of desired replicas changes, the operator provisions or removes resources such as persistent volumes, secrets, and config maps while managing streaming replication.
* Service updates: Kubernetes remains the single source of truth, ensuring that PostgreSQL service endpoints are always up to date.
* Rolling updates: When an image is updated, the operator follows a rolling strategy—first updating replica pods before performing a controlled switchover for the primary.

* CloudNativePG manages additional Kubernetes resources to enhance PostgreSQL management, including: `Backup`, 
* `ClusterImageCatalog`, `Database`, `ImageCatalog`, `Pooler`, `Publication`, `ScheduledBackup`, and `Subscription`.

## Simple Example

```yaml
#
# Example cloudnative-pg Cluster CRD
#
---
apiVersion: postgresql.cnpg.io/v1
kind: Cluster
metadata:
  name: my-cluster
  namespace: my-namespace
spec:
  # Ideally use at least 2 instances so that the PodDisruptionBudget does not prevent Node maintenance activities
  instances: 2

  # You can override the image (and version) of postgresql used here
  imageName: ghcr.io/cloudnative-pg/postgresql:17.4

  storage:
    # The size of the PVC that will get created for storing the PGDATA
    size: 1Gi
    # If you need your PVCs to be satisfied by a known storage class, you can set it here
    storageClass: my-special-storage-class
```

## Pod Anti-Affinity

By default, CloudNativePG configures cluster instances (Pods) to preferably be scheduled on different nodes.

Below is a snippet of the `podAntiAffinity` configuration for a Pod using CloudNativePG's defaults.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-cluster-1
  namespace: my-namespace
spec:
  affinity:
    podAntiAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - podAffinityTerm:
          labelSelector:
            matchExpressions:
            - key: cnpg.io/cluster
              operator: In
              values:
              - my-cluster
            - key: cnpg.io/podRole
              operator: In
              values:
              - instance
          topologyKey: kubernetes.io/hostname
        weight: 100
  containers:
  - name: postgres
    ...
    
```


## Database Volume expansion

If the underlying `StorageClass` used by the cluster supports volume expansion you can change the size requirement of the 
`Cluster`, and the operator applies the change to every PVC.

If the `StorageClass` supports online volume resizing, the change is immediately applied to the pods. 
If the underlying storage class doesn't support that, you must delete the pod to trigger the resize.

The best way to proceed is to delete one pod at a time, starting from replicas and waiting for each pod to be back up.

For more information see: [CloudNativePG | Storage | Volume Expansion](https://cloudnative-pg.io/docs/1.29/storage#volume-expansion)


## Importing existing Postgres databases

TODO

## Links & Further information

* [CloudNativePG - GitHub](https://github.com/cloudnative-pg/cloudnative-pg)
* [CloudNativePG - Documentation](https://cloudnative-pg.io/docs/1.29/)