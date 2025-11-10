---
title: kubectl
tags:
 - container
 - kubernetes
 - kubectl
---

Kubernetes provides a command line tool kubectl for communicating with a Kubernetes cluster's control plane, using the Kubernetes API.
<!--more-->

## Configuration

For configuration, kubectl looks for a file named config in the `$HOME/.kube` directory. 
You can specify other [kubeconfig](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/) 
files by setting the `KUBECONFIG` environment variable or by setting the `--kubeconfig` flag.

## kubectl Commands

There are _heaps_ of wonderful kubectl commands which can make life a little easier. 
See [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/) for an overview.


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

To view the logs for a pod use the commands below:

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

## Forcefully delete Resources

> **DANGER** - Forcefully deleting objects can leave resources in the cluster dangling.
> Use with extreme caution!

Sometimes when you want to delete a particular resource in Kubernetes, it gets stuck in a Terminating phase.
This is typically because the object's finalizer is no longer in the cluster.
This invalid state can come as a result of using tools like Helm which creates custom resources during installation and does not edit or remove them during an uninstall.

To force delete, you take the following steps:

1. Edit the Object e.g. `kubectl edit pod pod-name` or `kubectl edit customresource/name`
2. Remove delete the custom finalizers. If the finalizer has only a "kubernetes" finalizer then you can ignore it as it will be recreated if you remove it
3. Delete the object `kubectl delete customresource/name`

For more information see:
* https://github.com/odytrice/kubernetes/blob/master/force-delete.md

## Other Handy Commands

| Command                                                                                                  | Description                                                                             |
|----------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| `kubectl rollout restart deployment <deployment-name>`                                                   | Restart all of the pods in a deployment                                                 |
| `kubectl rollout restart statefulset <deployment-name>`                                                  | Restart all of the pods in a statefulset                                                |
| `kubectl scale deployment gogs --replicas=0`                                                             | Scale the pods to 0                                                                     |
| `kubectl scale deployment magicmirror --replicas=0 && kubectl scale deployment magicmirror --replicas=1` | Restart a deployment. Useful if you need to reload data from a ConfigMap or similar)    |
| `kubectl logs $(k get po \| grep magicmirror \| awk '{ print $1}') -c install-modules`                   | Show the logs for a specific (init) container                                           |
| `kubectl drain --ignore-daemonsets <node>`                                                               | Drain a node in preparation for a maintenance activity                                  |
| `kubectl uncordon <node>`                                                                                | Tell Kubernetes that it can resume scheduling new pods onto the node (post maintenance) |
| `kubectl cp my-pod:my-file my-file`                                                                      | Copy a file from a pod to the host                                                      | 
