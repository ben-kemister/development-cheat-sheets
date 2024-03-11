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
    metallb.universe.tf/allow-shared-ip: dns-svc # This needs to match the other SVC which needs to share the same IP address
    metallb.universe.tf/loadBalancerIPs: 192.168.10.6 # 
...
```
For more details see the [IP Address Sharing section](https://metallb.universe.tf/usage/#ip-address-sharing) of the 
MetalLB documentation.

