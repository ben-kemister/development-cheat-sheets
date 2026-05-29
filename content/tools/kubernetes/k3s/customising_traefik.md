---
title: Traefik Ingress Controller Customization
date: 2023-10-27
tags: [k3s, traefik, ingress-controller, kubernetes, helm]
---

# Customizing the Traefik Ingress Controller in K3s

This document outlines the technical process for customizing the Traefik Ingress Controller deployed within a K3s cluster. 
Customization is achieved by leveraging K3s' packaged component mechanism, specifically through the `HelmChartConfig` resource.

## ⚙️ Core Customization Mechanism: `HelmChartConfig`

K3s allows administrators to customize packaged components (like Traefik) by providing an additional `HelmChartConfig` manifest. 
This method allows overriding default chart values without directly editing the K3s server manifests.

### Implementation Details

1.  **Manifest Placement:** Create the `HelmChartConfig` manifest in the K3s server manifest directory:
    `/var/lib/rancher/k3s/server/manifests/`

2.  **Target Resource:** The `HelmChartConfig` must match the `name` and `namespace` of the deployed Traefik `HelmChart`.

3.  **Customization Fields:** The `HelmChartConfig` supports the following fields for overriding values:
    *   `spec.valuesContent`: Overrides complex default chart values using YAML file content.
    *   `spec.valuesSecrets`: Overrides complex default chart values using external Kubernetes Secrets.
    *   `spec.failurePolicy`: Can be set to `abort` to manually halt the Helm operation upon failure.

### 🛠️ Example: Overriding Traefik Configuration

To customize Traefik, such as updating the image, repository, and enabling specific ports, you would create a manifest like this:

```yaml
apiVersion: helm.cattle.io/v1
kind: HelmChartConfig
metadata:
  name: traefik
  namespace: kube-system
spec:
  valuesContent: |
    image: docker.io/library/traefik
    repository: docker.io/library/traefik
    tag: 3.3.5
    ports:
      web:
        forwardedHeaders:
          trustedIPs:
          - 10.0.0.0/8
```

## 🚀 Advanced Customization Topics

### Enabling Gateway API Support

If advanced traffic routing is required, you can enable support for the Kubernetes Gateway API by setting specific values in the `HelmChartConfig`.

```yaml
# /var/lib/rancher/k3s/server/manifests/k3s-traefik-config.yaml
apiVersion: helm.cattle.io/v1
kind: HelmChartConfig
metadata:
  name: traefik
  namespace: kube-system
spec:
  valuesContent: |
    providers:
      kubernetesGateway:
        enabled: true
```

### Value Precedence Order

Understanding the order of value application is critical for accurate customization. 
The chart values are applied in the following order, from least to greatest precedence (meaning the last item listed wins):

1. Chart default values
2. HelmChartConfig `spec.valuesContent`
3. HelmChartConfig `spec.valuesSecrets`
4. HelmChart `spec.valuesContent`
5. HelmChart `spec.valuesSecrets`
6. HelmChart `spec.set` (Highest Precedence)

> Note: Values set via HelmChart `spec.set` will always override any values provided through HelmChartConfig.

## 💡 Best Practices Summary

* **Source of Truth**: Always use HelmChartConfig to customize packaged components, rather than editing the default manifest files located in `/var/lib/rancher/k3s/server/manifests/`.
* **API Version**: The traditional Ingress API is still supported, but the Gateway API provides a more expressive and extensible way to manage service exposure.
* **Resource Management**: When troubleshooting, ensure the `HelmChartConfig` name matches the HelmChart resource name and is placed in the correct namespace (`kube-system` is typical for Traefik).