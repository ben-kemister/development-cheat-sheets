---
title: Deployments
tags:
  - container
  - kubernetes
  - workload
  - deployment
---

This page contain information about the use of Kubernetes [deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).
<!--more-->

## Deployment Strategies

### Recreate
This is a basic deployment pattern which simply shuts down all the old pods and replaces them with new ones. 
You define it by setting the `spec:strategy:type` section of your manifest to Recreate, like this:

```yaml
apiVersion: apps/v1
kind: Deployment
...
spec:
  replicas: 3
  strategy:
    type: Recreate
```

The `Recreate` strategy can result in downtime, because old pods are deleted before ensuring that new pods are rolled 
out with the new version of the application.