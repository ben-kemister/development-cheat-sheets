---
title: Ephemeral (debug) Containers
tags:
 - kubernetes
 - kubectl
 - debug
 - ephemeral
---

[Ephemeral containers](https://kubernetes.io/docs/concepts/workloads/pods/ephemeral-containers/) are a special type of 
container that runs temporarily in an existing Pod to accomplish user-initiated 
actions such as troubleshooting. 
<!--more-->
You use ephemeral containers to inspect/debug services rather than to build applications they were introduced in Kubernetes v1.25 [stable].

## Usage

You can use ephemeral containers to inspect/edit the contents of a running container.
This is very handy when the running container does not have the tools you need.

### Attach to a running container

The basic usage is `kubectl -n <NAMESPACE> debug -it <RUNNING_POD> --image=<EPHEMERAL_CONTAINER_IMAGE>`, for example:
```shell
kubectl -n my-namespace debug -it my-application-pod --image=busybox:1.35.0-glibc
```

### Mounting a volume

You can use a custom partial container spec to mount an existing volume in an ephemeral (debug) container.
For example:

```yaml
#
# Description: A custom partial container spec which can be used to mount an existing volume in an ephemeral (debug) container
#
# Usage:
#   kubectl -n <NAMESPACE> debug -it <RUNNING_POD> --image=busybox:1.35.0-glibc --custom=volumes/debug-mount.yaml
#
volumeMounts:
  - mountPath: /app/conf
    name: app-data
    # Unfortunately you can't use `subPath` with ephemeral containers
```


## Links & References

* [Ephemeral Containers | Kubernetes Documentation](https://kubernetes.io/docs/concepts/workloads/pods/ephemeral-containers/)