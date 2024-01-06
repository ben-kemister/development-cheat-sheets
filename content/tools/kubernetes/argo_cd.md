---
title: "Argo CD"
date: 2023-12-29T06:43:55+11:00
tags:
- kubernetes
- development
- ci_cd
- argo_cd
---

[Argo CD](https://argo-cd.readthedocs.io/en/stable/) is a declarative, GitOps continuous delivery tool for Kubernetes.
<!--more-->

## Sync Options

For more information see the [Argo CD Sync Options doco](https://argo-cd.readthedocs.io/en/stable/user-guide/sync-options/).

### Create Namespace

Add `CreateNamespace=true` following to your `syncOptions` array.

