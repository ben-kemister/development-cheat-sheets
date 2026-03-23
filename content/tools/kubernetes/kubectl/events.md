---
title: Events
tags:
 - kubernetes
 - kubectl
 - events
---

This page covers the use of [events](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_events/) with `kubectl`.
<!--more-->

## Events for a Namespace

Use the syntax `kubectl events -n <NAMESPACE>`, for example:

To show all the events in the 'testing' Namespace use:
```shell
$ kubectl events -n testing
LAST SEEN   TYPE     REASON      OBJECT                 MESSAGE
6m38s       Normal   Scheduled   Pod/my-test-pod   Successfully assigned testing/my-test-pod to <NODE_NAME>
```

## Events for a Resource

Use the syntax `kubectl events [-n <NAMESPACE>] --for <RESOURCE>/<RESOURCE_NAME>`, for example:

To show the events for the 'my-test-pod' Pod use:
```shell
$ kubectl events -n testing --for pod/my-test-pod
LAST SEEN   TYPE     REASON      OBJECT                 MESSAGE
6m38s       Normal   Scheduled   Pod/my-test-pod   Successfully assigned testing/my-test-pod to <NODE_NAME>
```

To show the events for the 'traefik' Service use:
```shell
$ kubectl events -n kube-system --for Service/traefik
LAST SEEN             TYPE     REASON         OBJECT            MESSAGE
60m (x351 over 60d)   Normal   nodeAssigned   Service/traefik   announcing from node "node-2" with protocol "layer2"
```