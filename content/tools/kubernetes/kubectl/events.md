---
title: Events
tags:
 - kubernetes
 - kubectl
 - events
---

This page covers the use of [events](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_events/) with `kubectl`.
<!--more-->

## Show events for a Resource

Use the syntax `kubectl events [-n <NAMESPACE>] --for <RESOURCE>/<RESOURCE_NAME>`, for example:

```shell
$ kubectl events -n kube-system --for Service/traefik
LAST SEEN             TYPE     REASON         OBJECT            MESSAGE
60m (x351 over 60d)   Normal   nodeAssigned   Service/traefik   announcing from node "node-2" with protocol "layer2"
```