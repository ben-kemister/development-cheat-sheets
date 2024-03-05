---
title: kube-prometheus
tags:
 - kubernetes
 - monitoring
 - prometheus
 - grafana
---

This page contains information about the installation, use and configuration of the 
[kube-prometheus](https://github.com/prometheus-operator/kube-prometheus/tree/release-0.13?tab=readme-ov-file) stack.
<!--more-->
The kube-promethus project/repository collects Kubernetes manifests, Grafana dashboards, 
and Prometheus rules combined with documentation and scripts to provide easy to operate end-to-end Kubernetes cluster 
monitoring with Prometheus using the Prometheus Operator.

## Deploying with Kustomize

You can deploy the kube-prometheus stack using _kustomize_ which I personally think is a better (and more transparent) way 
of deploying the stack into your cluster.

Below is an example `kustomization.yaml` for the deployment of kube-prometheus using [Argo CD](./argo_cd.) into a Kubernetes 
cluster which uses Traefik as the ingress controller (like [k3s](../kubernetes/k3s)):

```yaml
#
# Description: This is a Kustomize file for applying my changes to the default kube-prometheus manifests.
#
# To view the results use:
#   kubectl kustomize ./k3s-monitoring/
#     or
#   kubectl apply --dry-run=client -k ./k3s-monitoring/
#
#
# To apply run:
#   kubectl apply -k ./k3s-monitoring/
#
---
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

# Use the default 'monitoring' namespace
#namespace: monitoring

# Additional resources like ingress rules, cluster and repository secrets.
resources:
  #  The kustomization.yaml file lives in the root of the kube-prometheus git repo
  - github.com/prometheus-operator/kube-prometheus?ref=v0.13.0
  - manifests/grafana-ingress.yaml
  - manifests/prometheus-ingress.yaml

# My changes/patches to the default kube-prometheus manifests
patches:
  # Make sure to sync the Namespace & CRDs before anything else (to avoid sync failures)
  - target:
      kind: Namespace
    patch: |-
      - op: add
        path: /metadata/annotations/argocd.argoproj.io~1sync-wave
        value: -5
  # Make sure to sync the Namespace & CRDs before anything else (to avoid sync failures)
  # Use replace strategy for CRDs, as they're too big fit into the last-applied-configuration annotation that kubectl apply wants to use
  - target:
      kind: CustomResourceDefinition
    patch: |-
      - op: add
        path: /metadata/annotations/argocd.argoproj.io~1sync-wave
        value: -5
      - op: add
        path: /metadata/annotations/argocd.argoproj.io~1sync-options
        value: Replace=true
  # Patch the grafana network policy so that we can get to the UI
  - target:
      kind: NetworkPolicy
      name: grafana
    patch: |-
      - op: add
        path: /spec/ingress/-
        value:
          from:
            - namespaceSelector:
                matchLabels:
                  # The Traefik ingress controller which resides in another namespace
                  kubernetes.io/metadata.name: kube-system
          ports:
            - port: 3000
              protocol: TCP
  # Patch the prometheus network policy so that we can get to the UI
  - target:
      kind: NetworkPolicy
      name: prometheus-k8s
    patch: |-
      - op: add
        path: /spec/ingress/-
        value:
          from:
          - namespaceSelector:
              matchLabels:
                # The Traefik ingress controller which resides in another namespace
                kubernetes.io/metadata.name: kube-system
          ports:
          - port: 9090
            protocol: TCP
  # Reduce the Prometheus replicas to save on system resources in our cluster
  - target:
      kind: Prometheus
      name: k8s
    patch: |-
      - op: replace
        path: /spec/replicas
        value: 1
  # Reduce the Alertmanager replicas to save on system resources in our cluster
  - target:
      kind: Alertmanager
      name: main
    patch: |-
      - op: replace
        path: /spec/replicas
        value: 1
```

## Scrapping ServiceManagers from other namespaces

By default, kube-prometheus only watches the `monitoring`, `kube-system` and `default` namespaces. To get it to automatically
discover and scrape ServiceMonitors in other namespaces (like the `longhorn-system` namespace) we need to give it permissions
by deploying a `Role` and `RoleBinding`.

Once the `Role` and `RoleBinding` have been deployed you should see the target(s) turn up in Prometheus.

Below are example `Role` and `RoleBinding` for monitoring longhorn. Once created and a ServiceManager deployed the 
longhorn targets should be listed under `serviceMonitor/longhorn-system/longhorn-prometheus-servicemonitor`.

### Role

```yaml
#
# Description: Role to allow Prometheus to scrape metrics from the longhorn ServiceMonitor
#
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  labels:
    app.kubernetes.io/component: prometheus
    app.kubernetes.io/instance: k8s
    app.kubernetes.io/name: prometheus
    app.kubernetes.io/part-of: kube-prometheus
    app.kubernetes.io/version: 2.46.0
    argocd.argoproj.io/instance: kube-prometheus
  name: prometheus-k8s
  namespace: longhorn-system
rules:
  - apiGroups:
      - ""
    resources:
      - services
      - endpoints
      - pods
    verbs:
      - get
      - list
      - watch
  - apiGroups:
      - extensions
    resources:
      - ingresses
    verbs:
      - get
      - list
      - watch
  - apiGroups:
      - networking.k8s.io
    resources:
      - ingresses
    verbs:
      - get
      - list
      - watch
```

### RoleBinding

```yaml
#
# Description: RoleBinding which allows the prometheus-k8s ServiceAccount to use the prometheus-k8s role in the 
#               longhorn-system namespace.
#
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  labels:
    app.kubernetes.io/component: prometheus
    app.kubernetes.io/instance: k8s
    app.kubernetes.io/name: prometheus
    app.kubernetes.io/part-of: kube-prometheus
    app.kubernetes.io/version: 2.46.0
    argocd.argoproj.io/instance: kube-prometheus
  name: prometheus-k8s
  namespace: longhorn-system
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: prometheus-k8s
subjects:
  - kind: ServiceAccount
    name: prometheus-k8s
    namespace: monitoring
```

## References and Links

* [Prometheus on K3s - Post](../../posts/2024-01-03-Prometheus-on-k3s)