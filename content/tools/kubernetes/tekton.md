---
title: "Tekton"
tags:
- kubernetes
- development
- ci_cd
---

[Tekton](https://tekton.dev/) is a Kubernetes-native open-source framework for creating CI/CD systems, allowing developers to build, 
test, and deploy across cloud providers and on-premise systems.
<!--more-->

## Tekton Operator

* [Tekton Operator - GitHub](https://github.com/tektoncd/operator)
* [Tketon Config - GitHub](https://github.com/tektoncd/operator/blob/main/docs/TektonConfig.md)

## Pruner

The [Pruner](https://tekton.dev/docs/operator/tektonconfig/#pruner) auto clean up feature for the Tekton `pipelinerun` and `taskrun` resources. 
In the background pruner container runs `tkn` command.