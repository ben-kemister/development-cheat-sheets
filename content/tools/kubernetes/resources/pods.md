---
title: Pods
tags:
  - kubernetes
---

[Pods](https://kubernetes.io/docs/concepts/workloads/pods/) are the smallest deployable units of computing that you can create and manage in Kubernetes.
<!--more-->

## Node affinity

There are two types of [node affinity](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#node-affinity):

1. `requiredDuringSchedulingIgnoredDuringExecution` - The scheduler **can't schedule the Pod unless the rule is met**.
2. `preferredDuringSchedulingIgnoredDuringExecution` - The scheduler **tries to find a node that meets the rule**. 
   If a matching node is not available, the scheduler still schedules the Pod.

### preferredDuringSchedulingIgnoredDuringExecution

```yaml
spec:
  affinity:
    nodeAffinity:
      # Try to run this on a 'worker' node
      preferredDuringSchedulingIgnoredDuringExecution:
      - preference:
          matchExpressions:
          - key: node-role.kubernetes.io/master
            operator: DoesNotExist
        weight: 1
```