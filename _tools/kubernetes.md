---
tool: kubernetes
name: Kubernetes
tags:
 - container
 - linux
 - development
--- 

TBC
<!--more-->

# Kubernetes overview and major components

## Control Pane components

The control pane components are started with the container runtime (i.e. Docker, CRI-O )

## Creating/Updating API Resources - `Apply` vs `Create`

`apply` is preferred as this uses a declarative approach, this means that if you run it twice you will NOT get an error.

## Namespaces

default
kube-system       Used for the Kubernetes systems
kube-public       Available publicly, for use for things like container repositories

## Volumes

`nfs` is the only storage type that supports accessModes of `ReadWriteMany`.

## Services (and access)

`ClusterIP` only exposes the service(s) to the container.

`NodePort` is accessible from both within and outside the Kuberentes cluster.

## Config maps

Best practice is to inject your config map as a volume.

## Kubernetes Commands

Below are some of the useful commands

### Interactive shell into container

```sh
sudo k3s kubectl exec -it <name-of-pod> -- /bin/bash

# For example
sudo k3s kubectl exec -it task-pv-pod -- /bin/bash
```

### Top (resource usage)

```sh
# for the cluster nodes
kubectl top node

# for the pods
kubectl top pod
```

### logs

To view the logs for a pod use the commmads below:

```sh
kubectl logs <pod_name>

# For example
kubectl logs traefik-ingress-7f4f6cd549-jkdjl -n kube-system
```

#### Logs for a specific container

To access logs from a specific container (such as Init Containers)
Pass the Init Container name along with the Pod name to access its logs.

```sh
kubectl logs <pod-name> -c <init-container-2>

# For example
kubectl logs <pod-name> -c <init-container-2>

kubectl logs $(kubectl get po | grep magicmirror | awk '{ print $1}') -c install-modules
```

## Handy Commands

| Command | Description |
| --- | --- |
| `k scale deployment gogs --replicas=0` | Scale the pods to 0 |
| `k scale deployment magicmirror --replicas=0 && k scale deployment magicmirror --replicas=1` | Restart a deployment. Useful if you need to reload data from a ConfigMap or similar) |
| `k logs $(k get po | grep magicmirror | awk '{ print $1}') -c install-modules` | Show the logs for a specific (init) container |