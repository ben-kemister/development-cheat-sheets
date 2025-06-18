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
You define it by setting the `spec.strategy.type` section of your manifest to Recreate, like this:

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

## (ReplicaSet) Clean up

You can set `.spec.revisionHistoryLimit` field in a Deployment to specify how many old ReplicaSets for this Deployment you want to retain. 
The rest will be garbage-collected in the background. By default, it is 10.

The cleanup only starts **after** a Deployment reaches a complete state. 
If you set `.spec.revisionHistoryLimit` to 0, any rollout nonetheless triggers creation of a new ReplicaSet before Kubernetes removes the old one.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: it-tools
  annotations:
    kubernetes.io/description: "Provides the IT-Tools app"
spec:
  # Reduce the number of old ReplicaSets to retain
  revisionHistoryLimit: 3
  ...
```

For more information see: [Clean up Policy | Kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#clean-up-policy)