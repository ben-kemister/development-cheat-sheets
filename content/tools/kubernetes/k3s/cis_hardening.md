---
title: "CIS Hardening"
tags:
  - k3s
  - cis
  - hardening
---

This page contains information about hardening a k3s cluster.
<!--more-->



## Testing PodSecurityConfiguration

### Example of Warning

```shell
$ kubectl run tmp-shell --rm -i --tty --image redhat/ubi8
Warning: would violate PodSecurity "restricted:latest": allowPrivilegeEscalation != false (container "tmp-shell" must set securityContext.allowPrivilegeEscalation=false), unrestricted capabilities (container "tmp-shell" must set securityContext.capabilities.drop=["ALL"]), runAsNonRoot != true (pod or container "tmp-shell" must set securityContext.runAsNonRoot=true), seccompProfile (pod or container "tmp-shell" must set securityContext.seccompProfile.type to "RuntimeDefault" or "Localhost")
```

## Links & References

* [CIS Hardening Guide - k3s](https://docs.k3s.io/security/hardening-guide)