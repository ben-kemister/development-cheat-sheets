---
title: "2024 01 03 Prometheus on K3s"
date: 2024-01-03T08:13:26+11:00
draft: true
tags:
- kubernetes
- k3s
- monitoring
- prometheus
- traefik
- grafana
---

This post is about the installation and configuration of a Prometheus monitoring stack on my [k3s](../tools/kubernetes/k3s) cluster.
<!--more-->

I started using the information the [K3s Monitoring rpi4cluster.com page](https://rpi4cluster.com/monitoring/monitor-intro/), 
but found this to be out of date and the author wanted to step though each of the parts as a learning exercise. 
This was not my goals, so I switched to using the 
[kube-prometheus GitHub project](https://github.com/prometheus-operator/kube-prometheus/).

> Note the images used in this project come from a variety of image registries. You may need to check the `registry.yaml` 
> configuration of your cluster nodes to make sure they can retrieve the images required.

## Clone the repository

For a [quick start scenarios](https://github.com/prometheus-operator/kube-prometheus/#quickstart) the `kube-prometheus` 
developers have kindly created a folder containing all the pre-built
Kubernetes manifests ready to apply to a new cluster making it very easy.

To get access to these manifests clone the `kube-prometheus` GitHub repo:

```shell
git clone https://github.com/prometheus-operator/kube-prometheus.git
```

> Note the main `branch` targets new versions of the Kubernetes API (currently v1.27 and v1.28). 
> If you are working with an older version of Kubernetes see the [compatibility information](https://github.com/prometheus-operator/kube-prometheus/#compatibility).

## Apply CRDs and Namespace

Next we are going to create the `monitoring` Namespace and the `kube-prometheus` Custom Resource Definitions (CRDs), 
from the `kube-prometheus/` directory run:

```shell
kubectl apply --server-side -f manifests/setup
```

These can take a while to create, you can check on their status with:

```powershell
kubectl wait `
	--for condition=Established `
	--all CustomResourceDefinition `
	--namespace=monitoring
```
Which returns:
```text
customresourcedefinition.apiextensions.k8s.io/ingressroutes.traefik.containo.us condition met
customresourcedefinition.apiextensions.k8s.io/ingressroutetcps.traefik.containo.us condition met
customresourcedefinition.apiextensions.k8s.io/ingressrouteudps.traefik.containo.us condition met
customresourcedefinition.apiextensions.k8s.io/middlewaretcps.traefik.containo.us condition met
customresourcedefinition.apiextensions.k8s.io/serverstransports.traefik.containo.us condition met
customresourcedefinition.apiextensions.k8s.io/tlsoptions.traefik.containo.us condition met
customresourcedefinition.apiextensions.k8s.io/tlsstores.traefik.containo.us condition met
customresourcedefinition.apiextensions.k8s.io/traefikservices.traefik.containo.us condition met
customresourcedefinition.apiextensions.k8s.io/middlewares.traefik.containo.us condition met
customresourcedefinition.apiextensions.k8s.io/plans.upgrade.cattle.io condition met
customresourcedefinition.apiextensions.k8s.io/helmcharts.helm.cattle.io condition met
customresourcedefinition.apiextensions.k8s.io/helmchartconfigs.helm.cattle.io condition met
customresourcedefinition.apiextensions.k8s.io/addons.k3s.cattle.io condition met
customresourcedefinition.apiextensions.k8s.io/applications.argoproj.io condition met
customresourcedefinition.apiextensions.k8s.io/applicationsets.argoproj.io condition met
customresourcedefinition.apiextensions.k8s.io/appprojects.argoproj.io condition met
customresourcedefinition.apiextensions.k8s.io/alertmanagerconfigs.monitoring.coreos.com condition met
customresourcedefinition.apiextensions.k8s.io/alertmanagers.monitoring.coreos.com condition met
customresourcedefinition.apiextensions.k8s.io/podmonitors.monitoring.coreos.com condition met
customresourcedefinition.apiextensions.k8s.io/probes.monitoring.coreos.com condition met
customresourcedefinition.apiextensions.k8s.io/prometheuses.monitoring.coreos.com condition met
customresourcedefinition.apiextensions.k8s.io/prometheusagents.monitoring.coreos.com condition met
customresourcedefinition.apiextensions.k8s.io/prometheusrules.monitoring.coreos.com condition met
customresourcedefinition.apiextensions.k8s.io/scrapeconfigs.monitoring.coreos.com condition met
customresourcedefinition.apiextensions.k8s.io/servicemonitors.monitoring.coreos.com condition met
customresourcedefinition.apiextensions.k8s.io/thanosrulers.monitoring.coreos.com condition met
```

## Apply Kubernetes objects

Now we are going to create (apply) the remaining `kube-prometheus` objects with:

```shell
kubectl apply -f manifests/
```

This will create a lot of objects.

## (Basic & Temporary) UI Access

As described on the [Access UIs page](https://github.com/prometheus-operator/kube-prometheus/blob/main/docs/access-ui.md), 
the simplest way to access any of the UIs is to set port forwarding to your local machine.

For Prometheus:

```shell
kubectl --namespace monitoring port-forward svc/prometheus-k8s 9090
```

Then access via http://localhost:9090

For Grafana:

```shell
kubectl --namespace monitoring port-forward svc/grafana 3000
```

Then access via http://localhost:3000 and use the default grafana user:password of admin:admin.

## Permanent UI Access

You can set up more permanent access to the various UIs by updating/patching the `NetworkPolicy` applied by the 
`kube-prometheus` _quickstart_ manifests and then creating an `Ingress` object. 

The example below shows the process for creating access to the **grafana** UI.

### Patch NetworkPolicy

Create a `grafana-networkPolicy-patch.yaml` file with the given contents:

```yaml
#
# Description: A patch file to update the grafana NetworkPolicy to allow ingress from the Traefik ingress controller.
#
# To view results of patch:
#   kubectl -n monitoring patch --dry-run="server" NetworkPolicy grafana --patch-file .\grafana-networkPolicy-patch.yaml -o yaml
#
# To apply patch:
#   kubectl -n monitoring patch NetworkPolicy grafana --patch-file .\grafana-networkPolicy-patch.yaml
#
---
spec:
  ingress:
    - from:
        # A copy of the original podSelector block is included in this patch file 
        # so that it is not overwritten when applying the patch
        - podSelector:
            matchLabels:
              app.kubernetes.io/name: prometheus
        # Added to allow ingress from the Traefik ingress controller which resides in another namespace
        - namespaceSelector:
            matchLabels:
              # The label must match that of the namespace where the Traefik ingress controller runs
              kubernetes.io/metadata.name: kube-system
      ports:
        - port: 3000
          protocol: TCP
```

> Note that the default Traefik ingress controller pod lives in the `kube-system` namespace which is what the `namespaceSelector`
> `matchLabels` is targeting.

Apply this patch to the original `grafana` NetworkPolicy with the command:

```shell
kubectl -n monitoring patch NetworkPolicy grafana --patch-file .\grafana-networkPolicy-patch.yaml
# networkpolicy.networking.k8s.io/grafana patched
```

### Create Ingress

After updating the NetworkPolicy you then need to create an Ingress to allow access to the required Service (and the UI).
For the Grafana Service/Deployment, create a `grafana-ingress.yaml` file with the following contents:

```yaml
#
# Exposes the Grafana UI from the Prometheus stack
#
# Apply with:
#   kubectl apply -f ./grafana-ingress.yaml
#
---
kind: Ingress
apiVersion: networking.k8s.io/v1
metadata:
  name: Grafana-dashboard
  namespace: monitoring
  annotations:
    # Annotations to add TLS/certificate to https endpoint
    traefik.ingress.kubernetes.io/router.entrypoints: websecure
    traefik.ingress.kubernetes.io/router.tls.certresolver: letsencrypt--duckdns-resolver
spec:
  # Use the traefik ingress controller (required if it is not the default ingress class)
  ingressClassName: traefik
  rules:
    - host: "<YOUR_DNS_ENTRY>"
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: grafana
                port:
                  name: http
```

Then apply this to your cluster with the command:

```shell
kubectl apply -f ./grafana-ingress.yaml
```

The Grafana UI should now be accessible from the URL: `https://<YOUR_DNS_ENTRY>`

## (Optional) Reduce replicas to conserve resources

If you are only running a small cluster you may want to reduce the number of Alertmanager and Prometheus replicas to 
save some resources.

This can be done by patching the original `main` 'Alertmanager' and `k8s` 'Prometheus' objects created in the 
`kube-prometheus` _quickstart_ manifests and changing the number of replicas.

### Patch 'main' Alertmanager

Create a `alertmanager-alertmanager-patch.yaml` file with the following content:

```yaml
#
# Description: A patch file to reduce the replicas of the 'main' Alertmanager CRD
#
# To view results of patch:
#   kubectl -n monitoring patch --dry-run="server" --type merge alertmanager main --patch-file .\alertmanager-alertmanager-patch.yaml -o yaml
#
# To apply patch:
#   kubectl -n monitoring patch --type merge alertmanager main --patch-file .\alertmanager-alertmanager-patch.yaml
#
---
spec:
  replicas: 2
```

Then apply it with:

```shell
kubectl -n monitoring patch --type merge alertmanager main --patch-file .\alertmanager-alertmanager-patch.yaml
```

### Patch 'k8s' Prometheus

Create a `prometheus-prometheus-patch.yaml` file with the following contents:

```yaml
#
# Description: A patch file to reduce the replicas of the 'k8s' Prometheus CRD
#
# To view results of patch:
#   kubectl -n monitoring patch --dry-run="server" --type merge Prometheus k8s --patch-file .\prometheus-prometheus-patch.yaml -o yaml
#
# To apply patch:
#   kubectl -n monitoring patch --type merge Prometheus k8s --patch-file .\prometheus-prometheus-patch.yaml
#
---
spec:
  replicas: 1
```

Then apply the patch with:

```shell
kubectl -n monitoring patch --type merge Prometheus k8s --patch-file .\prometheus-prometheus-patch.yaml
```

## Done!

You should now have a working Prometheus monitoring stack running on your k3s Kubernetes cluster.

## References and Links

Below are the links to some of the references used for this post:

* [K3s Monitoring - rpi4cluster.com](https://rpi4cluster.com/monitoring/monitor-intro/)
* [kube-prometheus - GitHub](https://github.com/prometheus-operator/kube-prometheus/)
* [Access UIs - kube-prometheus](https://github.com/prometheus-operator/kube-prometheus/blob/main/docs/access-ui.md#access-uis)
* [Expose via Ingress - kube-prometheus](https://github.com/prometheus-operator/kube-prometheus/blob/main/docs/customizations/exposing-prometheus-alertmanager-grafana-ingress.md)
* [about network policy configuration - kube-prometheus](https://github.com/prometheus-operator/kube-prometheus/discussions/1907)
* [Network Policies - Kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/)
* [Kubectl Patch — Why we need and How to use it - Medium](https://foxutech.medium.com/kubectl-patch-why-we-need-and-how-to-use-it-416fec228802)