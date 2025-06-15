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

## Multi-line Commands

You can use the `command` and `args` blocks of a Pod to deal with multi line commands. 
This is handy for embedding scripting logic into a Pod manifest, for example:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-line-example-
  namespace: openhab
spec:
  containers:
  - name: copy-config
    command:
    - /bin/sh
    - -c
    # Here we pass in the multi-line 'script' as an argument
    args:
    - |
      echo "First Line"
      echo "Second Line"
      echo "Third Line"
    image: registry.access.redhat.com/ubi9-micro:9.6
    imagePullPolicy: IfNotPresent
  restartPolicy: Never
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

## Accessing the Host's Network

There are some situations where the Container/Pod needs to be able to access the same network as the Node/Host.

In this case you can use `hostNetwork` and `dnsPolicy: ClusterFirstWithHostNet`, for example:
```yaml
apiVersion: v1
kind: Pod
metadata:
   name: host-network-example
spec:
   
   hostNetwork: true
   #  For Pods running with hostNetwork, you should explicitly set its DNS policy to "ClusterFirstWithHostNet".
   # Otherwise, Pods running with hostNetwork and "ClusterFirst" will fallback to the behavior of the "Default" policy.
   dnsPolicy: ClusterFirstWithHostNet

   containers:
      - name: redhat
        image: registry.access.redhat.com/ubi9-micro:9.6
        imagePullPolicy: IfNotPresent
        command: [ "/bin/bash" ]
        tty: true
   terminationGracePeriodSeconds: 10
```

> Note that access to the `hostNetwork` requires a `privileged` Pod Security policy, which can be set on the Namespace 
> using labels, for example:
>   ```yaml
>         apiVersion: v1
>         kind: Namespace
>         metadata:
>           name: privileged-ns-test
>           labels:
>             # Set the `enforce` mode to use the `privileged` policy for this namespace
>             pod-security.kubernetes.io/enforce: privileged
>             pod-security.kubernetes.io/enforce-version: latest
>   ```

You can also specifically set the Pod's DNS configuration, for example:
```yaml


  dnsPolicy: "None"  # Don't use the host's DNS policy.
  dnsConfig:
    nameservers:
      - 10.8.0.10  # This should be the IP of your cluster's DNS service (kube-dns or core-dns).
    searches:
      - svc.cluster.local  # This is the default search domain.
```

For more information see [Pod's DNS Policy](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy)