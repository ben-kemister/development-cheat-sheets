---
tool: kubeclt
name: kubeclt
tags:
 - container
 - development
 - kubernetes
 - kubectl
--- 

TBC
<!--more-->

## kubectl Commands

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

## Handy Commands

| Command | Description |
| --- | --- |
| `kubectl scale deployment gogs --replicas=0` | Scale the pods to 0 |
| `kubectl scale deployment magicmirror --replicas=0 && kubectl scale deployment magicmirror --replicas=1` | Restart a deployment. Useful if you need to reload data from a ConfigMap or similar) |
| `kubectl logs $(k get po | grep magicmirror | awk '{ print $1}') -c install-modules` | Show the logs for a specific (init) container |
| `kubectl drain --ignore-daemonsets` <node> | Drain a node in preparation for a maintenance activity |
| `kubectl uncordon <node>` | Tell Kubernetes that it can resume scheduling new pods onto the node (post maintenance) |
| `kubectl cp my-pod:my-file my-file`| Copy a file from a pod to the host | 
| `kubectl get po -w -l app.kubernetes.io/instance=test-monitoring` | Watch a specific resource(s) based on a label |
