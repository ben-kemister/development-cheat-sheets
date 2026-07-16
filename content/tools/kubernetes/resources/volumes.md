---
title: Volumes
tags:
 - container
 - kubernetes
 - volumes
 - storage-class
---

This page contain information about the use of Kubernetes volumes (i.e. PVC & PV).
<!--more-->

## EmptyDir

`emptyDir` provides temporary storage for a Pod. It is created when a Pod is assigned to a node and exists as long as that Pod is running on that node. 
When the Pod is removed from the node (e.g., the Pod is deleted), the data in the `emptyDir` is permanently deleted.

### Use Cases
`emptyDir` is best suited for ephemeral data that does not need to persist beyond the lifecycle of the Pod:

* **Temporary Caching**: Ideal for applications requiring scratch space, such as NGINX cache or local download buffers.
* **Shared Memory**: Used to share a filesystem between multiple containers within the same Pod (e.g., for inter-process communication/IPC).
* **Ephemeral Logs**: For transient log files that do not require long-term retention.

### Limitations and Risks

While useful, `emptyDir` carries significant risks if not managed properly:

* **No Data Persistence** - Data is lost if the Pod is restarted or crashes. It is not suitable for any data that must survive a Pod lifecycle.
* **Uncontrolled Disk Usage** - By default, emptyDir volumes do not have a specified limit. A container writing excessively to an emptyDir can fill up the host node's disk, potentially leading to NoDiskPressure errors and affecting other workloads on the node.
* **Security Risks** - Storing sensitive or unencrypted data in an emptyDir can pose security risks if the underlying node storage is not properly secured.

### Implementing Size Limits

To prevent a Pod from consuming all available disk space on a node, you should always enforce a sizeLimit. 
If the storage used by the `emptyDir` exceeds the specified sizeLimit, Kubernetes will evict the Pod to protect the node's health.

The following example demonstrates how to implement a 100Mi limit on an `emptyDir` volume.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: safe-empty-dir-pod
spec:
   containers:
   - name: app-container
     image: alpine
     command: ["sh", "-c", "while true; do echo 'Writing temporary data...' >> /tmp/data.log; sleep 1; done"]
     volumeMounts:
      - name: tmp-volume
        mountPath: /tmp
        volumes:
   - name: tmp-volume
     emptyDir:
        sizeLimit: 100Mi
```

### Best Practices Summary
  
| Scenario                                  | Recommended Volume Type                                           |
|-------------------------------------------|-------------------------------------------------------------------|
| Persistent Data (Databases, user uploads) | 	PersistentVolume (PV) & PersistentVolumeClaim (PVC)              |
| Temporary Scratch Space                   | 	`emptyDir` with `sizeLimit` set                                  | 
| Inter-container Communication             | 	`emptyDir` (often using `medium: Memory` for RAM-backed storage) |


## Local (host) volume

```yaml
# A custom StorageClass for use with local (node) persistent storage
---
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
   name: local-persistent-storage
provisioner: kubernetes.io/no-provisioner
reclaimPolicy: Retain
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
```

```yaml
# For the persistent storage of Traefik data (certs etc.) on the local disc of the pi-server node
#
# Note: Due to the file permission restrictions around the acme.json (letsencrypt certs) file
#       I was not able to get Traefik to use the NAS via an NFS PV.
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: traefik-local
spec:
  capacity:
    storage: 100Mi
  # If not set defaults to Filesystem
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: local-persistent-storage
  local:
    # Note: this directory needs to exist on the node selected from the expression below
    path: /var/local/k3s/traefik
  nodeAffinity:
    required:
      nodeSelectorTerms:
        - matchExpressions:
            - key: kubernetes.io/hostname
              operator: In
              values:
                - pi-server
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
   name: traefik-local
   namespace: kube-system
spec:
   # Must match the storageClassName in the PersistentVolume
   storageClassName: local-persistent-storage
   # The access modes must match those in a PersistentVolume for it to get Bound
   accessModes:
      - ReadWriteOnce
   resources:
      requests:
         storage: 100Mi
```

## Simple NFS Volume

> Apparently `nfs` is the only (native?) storage type that supports accessModes of `ReadWriteMany`.

```yaml
#
# A custom StorageClass for use with NAS persistent storage
#
# To apply: 
#   kubectl apply -f ./nas-nfs-persistent-sc.yaml
#
---
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: nas-nfs-persistent
provisioner: kubernetes.io/no-provisioner
reclaimPolicy: Retain
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
```

```yaml
#
# The PV for the influxdb
#
# kubectl apply -f .\influxdb-volume.yaml
#
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: influxdb-nfs
  labels:
    database: influx
spec:
  capacity:
    # Allow 1 Gi of storage on the NAS
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: nas-nfs-persistent
  nfs:
    server: <NAS_IP_ADDRESS>
    path: "/volume1/volumes/influxdb_data"
  mountOptions:
    - nfsvers=4.0
---
#
# Note: as the 'nas-persistent-storage' has volumeBindingMode: WaitForFirstConsumer
# This PVC will not bind until a container starts to use it.
#
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: influxdb-nfs
  namespace: default
spec:
  # Must match the storageClassName in the PersistentVolume
  storageClassName: nas-nfs-persistent
  # The access modes must match those in a PersistentVolume for it to get Bound
  accessModes:
    - ReadWriteOnce
  selector:
    matchLabels:
      database: influx
  resources:
    requests:
      storage: 1Gi
```

## Host mounted CIFS Volume

You can mount a CIFS volume on a cluster node and expose it as a local (host) volume to containers running on the node.

This can be used as a hacky workaround to containers which do commands like `chown` the mounted volumes which can cause 
issues with NFS volumes.

> Note a much better solution is to use a cloud native storage for Kubernetes like [longhorn](../longhorn).

### Host preparation

1. `sudo apt install cif-utils`
2. `sudo mkdir -p /mnt/<NAS_NAME>/volumes/container_data`
3. `nano ~/.smbcredentials`
    ```text
    username=<username>
    password=<password>
    ```
4. `nano /etc/fstab`
    ```text
    //<YOUR_NAS_FQDN_NAME>/volumes/container_data /mnt/NAS_NAME/volumes/container_data cifs credentials=/home/<USER_ID>/.smbcredentials,rw,file_mode=0777,dir_mode=0777,addr=<NAS_IP_ADDRESS>,nounix,noserverino,vers=2.0 0 0
    ```
5. `sudo mount -a`

### Node Labels (for PV mapping)

This solution relies upon Persistent Volumes (PV), which use a `local` path on the node/host (which is mapped to CIFS share),
and use node label(s) to determine where these PV (and the pods that use them) should be created.

* Adding the label to a node - `kubectl label nodes <NODE_NAME> nas.volume/mediawiki=true=true`
* Viewing the labels on a node - `kubectl label --list nodes <NODE_NAME>`

### PV & PVC

```yaml
# A custom StorageClass for use with local (node) persistent storage
---
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
   name: local-persistent-storage
provisioner: kubernetes.io/no-provisioner
reclaimPolicy: Retain
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
```

```yaml
#
# For the persistent storage of mediawiki data.
#
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mediawiki-local
spec:
  capacity:
    storage: 1Gi
  # If not set defaults to Filesystem
  volumeMode: Filesystem
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  storageClassName: local-persistent-storage
  local:
    # Note: this directory needs to exist on the node selected from the expression below
    path: /mnt/<NAS_NAME>/volumes/container_data
  nodeAffinity:
    required:
      nodeSelectorTerms:
        - matchExpressions:
            - key: "nas.volume/mediawiki"
              operator: Exists
---
#
# Note: as the 'nas-persistent-storage' has volumeBindingMode: WaitForFirstConsumer
# This PVC will not bind until a container starts to use it.
#
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mediawiki-local
  namespace: default
spec:
  volumeName: mediawiki-local
  # Must match the storageClassName in the PersistentVolume
  storageClassName: local-persistent-storage
  # The access modes must match those in a PersistentVolume for it to get Bound
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Gi
```
