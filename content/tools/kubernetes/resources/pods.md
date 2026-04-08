---
title: Pods
tags:
  - kubernetes
---

[Pods](https://kubernetes.io/docs/concepts/workloads/pods/) are the smallest deployable units of computing that you can create and manage in Kubernetes.
<!--more-->

## Environmental Variables

### Sourcing Environment Variables from ConfigMaps or Secrets

Use `envFrom` to define all of a ConfigMap's data as container environment variables. 
The key from the ConfigMap becomes the environment variable name in the Pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: dapi-test-pod
spec:
  containers:
    - name: test-container
      image: registry.k8s.io/busybox:1.27.2
      command: [ "/bin/sh", "-c", "env" ]
      envFrom:
      - configMapRef:
          name: special-config
      - secretRef:
          name: my-secret
  restartPolicy: Never
```

#### Environment Variable Precedence

In a Kubernetes Pod, environment variables follow a specific order of precedence when the same key is defined in multiple 
locations. The order of precedence from **highest** to _lowest_ is:
1. `env` Field: Variables defined directly in the env list within the container specification have the highest priority. 
   They override values from all other sources.
2. `envFrom` Field: Variables sourced from ConfigMaps or Secrets via envFrom come next.
  > Within `envFrom`: If multiple sources (e.g., two ConfigMaps) are listed, the last one defined in the list takes 
  > precedence if there are duplicate keys.
3. Container Image: Variables defined using the `ENV` instruction in the Dockerfile/Containerfile (i.e. the container image) 
  have the lowest priority and are overridden by any values set in the Pod manifest.


## Overriding a Pods' Command

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

## Affinity and Anti-affinity

### Node Affinity

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

### Node Anti-affinity

To ensure two Kubernetes Pods are scheduled on different nodes, you must use Pod Anti-Affinity with the `kubernetes.io/hostname` 
topology key. 
This configuration tells the scheduler not to place a pod on a node that is already running a pod with specific labels.

The key fields for creating anti-affinity rules are:
* `labelSelector` - Identifies which pods to avoid. Typically, you use the pod's own label to prevent multiple replicas of the same application from sharing a node.
* `topologyKey: "kubernetes.io/hostname"` - This is the standard label used to represent individual nodes. It ensures that the "repulsion" rule applies at the node level.
* `requiredDuringSchedulingIgnoredDuringExecution` - A "hard" rule. Use this if you absolutely require separate nodes for High Availability.
* `preferredDuringSchedulingIgnoredDuringExecution` - A "soft" rule. Use this if you prefer different nodes but allow co-location if the cluster is full.

#### Hard Constraint

The following manifest uses a Hard Constraint (`requiredDuringSchedulingIgnoredDuringExecution`), meaning the scheduler 
will only place the pod on a different node. If no such node is available, the pod will remain in a `Pending` state.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
  labels:
    app: my-app  # Label used for anti-affinity matching
spec:
  affinity:
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app.kubernetes.io/name # This key can be any label on the Pod
            operator: In
            values:
            - my-app
        topologyKey: "kubernetes.io/hostname"
  containers:
  - name: my-container
    image: nginx
```

#### Soft Constraint

The following manifest uses a Hard Constraint (`preferredDuringSchedulingIgnoredDuringExecution`), meaning the scheduler 
_tries_ to find a node that meets the rule. If a matching node is not available, the scheduler still schedules the Pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
  labels:
    app: my-app  # Label used for anti-affinity matching
spec:
  affinity:
    podAntiAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        podAffinityTerm:
          labelSelector:
            matchExpressions:
              - key: app.kubernetes.io/name
                operator: In
                values:
                  - my-app
          topologyKey: "kubernetes.io/hostname"
  containers:
  - name: my-container
    image: nginx
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

## Adding entries to the Pod's `/etc/hosts` file

You can add custom entries to the Pod's `/etc/hosts` file using the `hostAliases` section of the Pod's resource manifest.
For example:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hostaliases-pod
spec:
  restartPolicy: Never
  hostAliases:
  - ip: "127.0.0.1"
    hostnames:
    - "foo.local"
    - "bar.local"
  - ip: "10.1.2.3"
    hostnames:
    - "foo.remote"
    - "bar.remote"
  containers:
  - name: cat-hosts
    image: busybox:1.28
    command:
    - cat
    args:
    - "/etc/hosts"
```

For more information see: [Adding entries to Pod /etc/hosts with HostAliases](https://kubernetes.io/docs/tasks/network/customize-hosts-file-for-pods/)
