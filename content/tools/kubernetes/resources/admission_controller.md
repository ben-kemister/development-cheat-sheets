---
title: Admission Controller
tags:
  - kubernetes
---

Kubernetes provides a built-in [admission controller](https://kubernetes.io/docs/tasks/configure-pod-container/enforce-standards-admission-controller/#configure-the-admission-controller) 
to enforce the [Pod Security Standards](https://kubernetes.io/docs/concepts/security/pod-security-standards/). 
<!--more-->
You can configure this admission controller to set cluster-wide defaults and [exemptions](https://kubernetes.io/docs/concepts/security/pod-security-admission/#exemptions).

## Pod Security Standards

The [Pod Security Standards](https://kubernetes.io/docs/concepts/security/pod-security-standards/) define different 
policies to broadly cover the security spectrum. 
These policies are cumulative and range from highly-permissive to highly-restrictive, these are:

* `privileged` - Unrestricted policy, providing the widest possible level of permissions. This policy allows for known privilege escalations.
* `baseline` - Minimally restrictive policy which prevents known privilege escalations. Allows the default (minimally specified) Pod configuration.
* `restricted` - Heavily restricted policy, following current Pod hardening best practices.

## Set Default Pod Security Standards (the Built-in Admission Controller)

Kubernetes provides a built-in admission controller to enforce the [Pod Security Standards](https://kubernetes.io/docs/concepts/security/pod-security-standards/). 
You can configure this admission controller to set cluster-wide defaults and [exemptions](https://kubernetes.io/docs/concepts/security/pod-security-admission/#exemptions).

An example can be found below:
```yaml
apiVersion: apiserver.config.k8s.io/v1
kind: AdmissionConfiguration
plugins:
- name: PodSecurity
  configuration:
    apiVersion: pod-security.admission.config.k8s.io/v1 # see compatibility note
    kind: PodSecurityConfiguration
    # Defaults applied when a mode label is not set.
    #
    # Level label values must be one of:
    # - "privileged" (default)
    # - "baseline"
    # - "restricted"
    #
    # Version label values must be one of:
    # - "latest" (default) 
    # - specific version like "v1.30"
    defaults:
      enforce: "baseline"
      enforce-version: "latest"
      audit: "restricted"
      audit-version: "latest"
      warn: "restricted"
      warn-version: "latest"
    exemptions:
      # Array of authenticated usernames to exempt.
      usernames: []
      # Array of runtime class names to exempt.
      runtimeClasses: []
      # Array of namespaces to exempt.
      namespaces:
      #  Kubernetes critical additions such as CNI, DNS, and Ingress are run as pods in the kube-system namespace.
      # Therefore, this namespace often needs to have a policy that is less restrictive so that these components can run properly.
      - kube-system
```