---
title: Authentication
tags:
- openshift
- authentication
---

OpenShift [authentication](https://docs.redhat.com/en/documentation/openshift_container_platform/latest/html/authentication_and_authorization/overview-of-authentication-authorization) 
determines a user's identity to ensure only approved actors can access the cluster. 
<!--more-->
It is primarily handled by a built-in OAuth server that validates credentials against external identity providers and 
issues access tokens for the OpenShift API.

## Overview

The core of OpenShift's authentication revolves around the Authentication Operator, which manages the `oauth-openshift` 
Pods in the `openshift-authentication` Namespace that operate as the internal OAuth2 authentication server. 
These Pods delegate credential verification to external identity providers like LDAP or Microsoft Active Directory (AD).

## Debugging

Under default configurations, the Authentication Operator strictly enforces a `Normal` log level which deliberately 
suppresses verbose directory traffic.
This is great for reducing noisy logs and preventing credential leakage, but it makes diagnosing a basic LDAP/authentication
issue nearly impossible.

To expose the underlying LDAP transactions, the Authentication Operator’s configuration must be mutated to instruct the 
`oauth-openshift` Pods to emit highly verbose execution paths. This involves patching the cluster's global 
`authentications.operator.openshift.io` Custom Resource (CR).

To elevate the log level to `Debug`, execute a merge patch:
```shell
oc patch authentications.operator.openshift.io/cluster --type=merge -p '{"spec":{"logLevel":"Debug"}}'

```

This will trigger a rolling update of the `oauth-openshift` Pods. Once the new Pods reach a `Ready` state, you can filter 
the output to isolate the LDAP traffic:
```shell
oc logs -f -l app=oauth-openshift -n openshift-authentication | grep -v -e healthz -e metrics
```

> Remember to revert the logging to `Normal` once you have finished debugging.

For additional information see:
* [Why Can’t My Users Log In? A Systematic Guide to OpenShift 4 LDAP Debugging](https://medium.com/@tcij1013/why-cant-my-users-log-in-a-systematic-guide-to-openshift-4-ldap-debugging-0b6d86463dde)
* [Troubleshooting OpenShift Container Platform 4.x: LDAP authentication](https://access.redhat.com/articles/6990472)
* [Enable debug loglevel for openshift-authentication pods in OpenShift Container Platform 4 Cluster](https://access.redhat.com/solutions/4100741)
