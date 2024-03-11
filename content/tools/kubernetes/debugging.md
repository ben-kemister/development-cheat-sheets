---
title: Debugging
tags:
- kubernetes
- kubectl
- debugging
- containers
---

This page contains information about how to debug pods/containers in Kubernetes.
<!--more-->

## Helpful images

The images below can be used to assist with diagnosing network/connectivity issues within Kubernetes

| Image               | Registry   | Notes                                                                                                                                   |
|---------------------|------------|-----------------------------------------------------------------------------------------------------------------------------------------|
| `curlimages/curl`   | Docker Hub | The official curl image, seems to be regularly maintained (and less than 5MB, too!)                                                     | 
| `redhat/ubi8`       | Docker Hub | Red Hat’s Universal Base Image, basically a containerised version of Red Hat Enterprise Linux. (And helpfully, it contains curl.)       |
| `nicolaka/netshoot` | Docker Hub | a Docker + Kubernetes network trouble-shooting swiss-army container ([github](https://github.com/nicolaka/netshoot?tab=readme-ov-file)) |

## Debugging with an ephemeral container

[Ephemeral containers](https://kubernetes.io/docs/tasks/debug/debug-application/debug-running-pod/#ephemeral-container) 
are useful for interactive troubleshooting when `kubectl exec` is insufficient because a container has crashed or a 
container image doesn't include debugging utilities.

### Throw away pod

You can spin up a new pod containing your _debugging_ image of choice with:

```shell
kubectl run tmp-shell --rm -i --tty --image nicolaka/netshoot
```

### Attach the container to an existing pod

```shell
kubectl debug mypod -it --image=nicolaka/netshoot
```



