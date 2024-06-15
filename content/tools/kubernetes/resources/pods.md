---
title: Pods
tags:
  - kubernetes
---

[Pods](https://kubernetes.io/docs/concepts/workloads/pods/) are the smallest deployable units of computing that you can create and manage in Kubernetes.
<!--more-->

## Overriding Command

Set the `command` block to override the default command set in the image. This can be useful for fault-finding and diagnostics. 

```yaml
#
# To apply:
#   kubectl apply -n default -f ./alpine-pod.yaml
#
# To delete:
#   kubectl delete -n default -f ./alpine-pod.yaml
#
---
apiVersion: v1
kind: Pod
metadata:
   name: alpine-pod
spec:
   containers:
      - name: alpine
        image: "alpine:3.20.0"
        securityContext:
           allowPrivilegeEscalation: false
           capabilities:
              drop:
                 - ALL
           runAsUser: 1000
           runAsNonRoot: true
           seccompProfile:
              type: RuntimeDefault
        command: ['sh', '-c', 'echo "Hello, from Kubernetes!" && ls -la / && sleep 3600']
```

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