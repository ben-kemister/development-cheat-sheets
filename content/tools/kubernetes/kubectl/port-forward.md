---
title: Port Forward
tags:
 - kubernetes
 - kubectl
 - port-forward
 - network
---

kubectl [port-forward](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward) is a Kubernetes 
command-line tool that allows you to establish a secure connection between your local machine and a pod or service within a Kubernetes cluster. 
<!--more-->
This enables you to access applications running inside the cluster as if they were running on your own machine.

Essentially, it creates a tunnel that forwards traffic from a local port on your machine to a specific port on a pod in the cluster. 
This is useful for debugging, testing, or accessing services during development, without the need to expose those services publicly.


## Usage

```shell
kubectl port-forward TYPE/NAME [options] [LOCAL_PORT:]REMOTE_PORT [...[LOCAL_PORT_N:]REMOTE_PORT_N]
```

## Connect to a specific Pod in a namespace

To connect to a Pod called `my-pod-0` on port `8080` in `my-namespace` Namespace use the command:

```shell
kubectl -n my-namespace port-forward pod/my-pod-0 18080:8080
```

You can then use port `18080` on the local machine to access port `8080` in the Pod.
For example:

```powershell
PS> Invoke-WebRequest -Uri http://localhost:19001      


StatusCode        : 200
StatusDescription : OK
Content           : ...
```


## Links & References

* [Use Port Forwarding to Access Applications in a Cluster | Kubernetes Documentation](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/)