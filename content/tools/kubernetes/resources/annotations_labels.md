---
title: Annotations and Labels
tags:
- kubernetes
- annotations
- labels
---

This page contains information about Kubernetes [Annotations](https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/) 
and [Labels](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/).
<!--more-->

## Annotations

Kubernetes annotations are used to attach arbitrary, non-identifying metadata to Kubernetes objects. 
This metadata is not used for selection or identification of objects by Kubernetes itself, but it can be used by tools, 
libraries, and other clients to store information about the object.

## Labels

Kubernetes labels are key-value pairs that are attached to Kubernetes objects like pods, services, and deployments. 
They act as metadata, allowing you to organize, select, and manage these objects based on their attributes. 
Labels don't directly affect how the system operates but provide a powerful way for users to categorize and group objects for various purposes.

## Labels vs. Annotations (Selection vs Information)

Labels are used to select and identify objects, 
annotations are for attaching arbitrary metadata that Kubernetes doesn't use for identification or selection.

## Handy Annotations

### kubernetes.io/description

This annotation can be used on All Objects and is used for describing the specific behaviour of given object.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
  annotations:
    kubernetes.io/description: "Manages the 'my-app' application Pods"
spec:
...
```