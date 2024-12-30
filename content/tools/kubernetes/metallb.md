---
title: metallb
tags:
- kubernetes
- networking
---

[MetalLB](https://metallb.universe.tf/) is a load-balancer implementation for bare metal Kubernetes clusters, using standard routing protocols.
<!--more-->

## IP Address Pools 

```yaml
#
# Description: This is the default IPAddressPool used by metallb
#
---
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: default-pool
  namespace: metallb-system
spec:
  addresses:
    - 192.168.10.50-192.168.10.60 # IP range
```

Or for a single (protected) IP address:

```yaml
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: dns-pool
  namespace: metallb-system
spec:
  addresses:
    - 192.168.10.6/32 # Single IP
  autoAssign: false # Prevents the automatic allocation of IP addresses from this pool
```

## L2 Advertisement

```yaml
---
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: default
  namespace: metallb-system
spec:
  ipAddressPools:
    - default-pool
    - dns-pool
```

## Service Annotations

### IP address sharing

Done using annotations on the Service:

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    metallb.io/allow-shared-ip: dns-svc # This needs to match the other SVC which needs to share the same IP address
    metallb.io/loadBalancerIPs: 192.168.10.6 # 
...
```

> Note the older `metallb.universe.tf/xxx` annotations have been deprecated and replaced with `metallb.io/xxx`

For more details see the [IP Address Sharing section](https://metallb.universe.tf/usage/#ip-address-sharing) of the 
MetalLB documentation.

## Troubleshooting

### Which node is advertising the IP address

You can look at the events of the **LoadBalancer** `service` of the cluster, as per the [MetalLB doco](https://metallb.universe.tf/troubleshooting/#troubleshooting-service-advertisements).

```shell
# Find the LoadBalancer service
$ kubectl -n kube-system get svc | grep -E 'NAME|LoadBalancer'
NAME                TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)       AGE
traefik             LoadBalancer   10.43.78.121   192.168.10.60   <REDACTED>   68d
```

```shell
# Look at the events for that service
$ kubectl events -n kube-system --for Service/traefik
LAST SEEN             TYPE     REASON         OBJECT            MESSAGE
60m (x351 over 60d)   Normal   nodeAssigned   Service/traefik   announcing from node "node-2" with protocol "layer2"
```

