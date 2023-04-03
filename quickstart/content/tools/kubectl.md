---
title: kubeclt
tags:
 - container
 - development
 - kubernetes
 - kubectl
--- 

Kubernetes provides a command line tool _**kubectl**_ for communicating with a Kubernetes cluster's control plane, using the Kubernetes API.
<!--more-->
For configuration, kubectl looks for a file named config in the `$HOME/.kube` directory. 
You can specify other [kubeconfig](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/) 
files by setting the `KUBECONFIG` environment variable or by setting the `--kubeconfig` flag.

## kubectl Commands

There are heaps of wonderful kubectl commands which can make life a little easier. 
See [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/) for an overview.

### Get <Resource>

| Command                                                                                  | Description                                   |
|------------------------------------------------------------------------------------------|-----------------------------------------------|
| `kubectl get po -w -l app.kubernetes.io/instance=test-monitoring`                        | Watch a specific resource(s) based on a label |
| `kubectl get po -l app.kubernetes.io/name=openhab --field-selector status.phase=Running` | Get a running pod based on a label            |

kubectl get po -l app.kubernetes.io/name=openhab --field-selector status.phase=Running

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

### Custom columns
Using the flag `-o custom-columns=<header-name>:<field>` will let you customize the output.

Example with resource name, under header `NAME`
`kubectl get pods -o custom-columns=NAME:metadata.name`
output
```shell
NAME
myapp-5b77df6c48-dbvnh
myapp-64d5985fdb-mcgcn
httpd-9497b648f-9vtbl
```

### Omit headers
The proper solution to omit the header line is by using the flag `--no-headers`

`kubectl get pods -o custom-columns=NAME:metadata.name --no-headers`
Example output
```shell
myapp-5b77df6c48-dbvnh
myapp-64d5985fdb-mcgcn
httpd-9497b648f-9vtbl
```

## Other Handy Commands

| Command | Description |
| --- | --- |
| `kubectl scale deployment gogs --replicas=0` | Scale the pods to 0 |
| `kubectl scale deployment magicmirror --replicas=0 && kubectl scale deployment magicmirror --replicas=1` | Restart a deployment. Useful if you need to reload data from a ConfigMap or similar) |
| `kubectl logs $(k get po | grep magicmirror | awk '{ print $1}') -c install-modules` | Show the logs for a specific (init) container |
| `kubectl drain --ignore-daemonsets` <node> | Drain a node in preparation for a maintenance activity |
| `kubectl uncordon <node>` | Tell Kubernetes that it can resume scheduling new pods onto the node (post maintenance) |
| `kubectl cp my-pod:my-file my-file`| Copy a file from a pod to the host | 
