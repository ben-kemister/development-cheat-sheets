---
title: "Longhorn on K3s"
date: 2024-02-25T17:32:55+11:00
draft: false
tags:
- kubernetes
- k3s
- longhorn
- prometheus
- grafana
- traefik
---

This post covers the installation of [longhorn](https://longhorn.io/) on a k3s cluster.
<!--more-->
There a multiple ways to install Longhorn, but in this post we are going to rely upon ArgoCD to manage the installation 
and configuration of longhorn within the cluster.

# Cluster configuration

The k3s cluster uses [Traefik](../tools/traefik.md) as the ingress controller and uses [cert-manager](../tools/kubernetes/cert-manager) for certificate generation and
management.

For relates posts on these components see:
* [k3s with customised Traefik Ingress](./2022-04-08-K3s-with-customised-Traefik-ingress)
* [Argo CD on k3s](./2023-12-29-Argo-CD-on-k3s)
* [cert-manager with Traefik on k3s](./2024-01-06-Cert-manager-with-Traefik-on-k3s)
* [Prometheus on k3s](2024-01-03-Prometheus-on-k3s)

## Installing Longhorn prerequisites

### Installing open-iscsi

Longhorn provides an iscsi installer to make it easier for users to install `open-iscsi` automatically:

```shell
kubectl apply -f https://raw.githubusercontent.com/longhorn/longhorn/v1.6.0/deploy/prerequisite/longhorn-iscsi-installation.yaml
```

Once pods have completed the DaemonSet can be deleted.

```shell
kubectl delete DaemonSet longhorn-iscsi-installation
```

### Installing NFSv4 client

Longhorn also provides a nfs installer to make it easier for users to install `nfs-client` automatically:

```shell
kubectl apply -f https://raw.githubusercontent.com/longhorn/longhorn/v1.6.0/deploy/prerequisite/longhorn-nfs-installation.yaml
```

Once pods have completed the DaemonSet can be deleted.

```shell
kubectl delete DaemonSet longhorn-nfs-installation
```

## Install using Argo CD

The [install with ArgoCD Longhorn documentation](https://longhorn.io/docs/1.6.0/deploy/install/install-with-argocd/) 
uses the longhorn helm chart which is a really easy way to install (and configure) longhorn.

To install you will need to create an Argo CD application manifest:

```yaml
#
# Description: Argo CD application for the installation of longhorn
# 
# Helm values information:
# - https://longhorn.io/docs/1.6.0/references/helm-values/
# - https://github.com/longhorn/longhorn/blob/master/chart/values.yaml
#
# To apply run:
#   kubectl.exe apply -f .\longhorn-application.yaml
#
---
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: longhorn
  namespace: argocd
spec:
  project: default
  sources:
    - chart: longhorn
      repoURL: https://charts.longhorn.io/
      targetRevision: v1.6.0 # Replace with the Longhorn version you'd like to install or upgrade to
      helm:
        valuesObject:
          preUpgradeChecker:
            jobEnabled: false
  destination:
    server: https://kubernetes.default.svc
    namespace: longhorn-system
  syncPolicy:
    automated:
      prune: true
    syncOptions:
      - CreateNamespace=true
```

### Customizing Default Settings

As we are using Helm and Argo CD to install Longhorn we can supply our own Helm `my-values.yaml` file.

Information on the default helm values can be found [here](https://longhorn.io/docs/1.6.0/references/helm-values/) and a
copy of the default `values.yaml` file can be found on [GitHub here](https://raw.githubusercontent.com/longhorn/charts/master/charts/longhorn/values.yaml).

Below is an example of a helm `my-values.yaml` file which customises some of the longhorn default settings:

```yaml
defaultSettings:
  #
  # Information on the default settings can be found:
  # - https://github.com/longhorn/longhorn/blob/master/chart/values.yaml
  # - https://github.com/longhorn/longhorn/tree/master/chart
  #
  allowCollectingLonghornUsageMetrics: false
  # -- Default number of replicas for volumes created using the Longhorn UI. For Kubernetes configuration, modify the `numberOfReplicas` field in the StorageClass. The default value is "3".
  defaultReplicaCount: 1
  # -- Setting that allows scheduling on disks with existing healthy replicas of the same volume. This setting is enabled by default.
  replicaDiskSoftAntiAffinity: false
  # Default data locality. A Longhorn volume has data locality if a local replica of the volume exists on the same node as the pod that is using the volume.
  # (Options: "disabled", "best-effort")
  defaultDataLocality: "best-effort"
  # -- Maximum snapshot count for a volume. The value should be between 2 to 250
  snapshotMaxCount: 5
  # -- Maximum number of successful recurring backup and snapshot jobs to be retained. When the value is "0", a history of successful recurring jobs is not retained.
  recurringSuccessfulJobsHistoryLimit: 5
  # -- Maximum number of failed recurring backup and snapshot jobs to be retained. When the value is "0", a history of failed recurring jobs is not retained.
  recurringFailedJobsHistoryLimit: 5
  # -- Maximum number of snapshots or backups to be retained.
  recurringJobMaxRetention: 10
  # Endpoint used to access the backupstore. (Options: "NFS", "CIFS", "AWS", "GCP", "AZURE")
  backupTarget: nfs://<NAS_HOST>:/volume1/Backup/longhorn
#  backupTargetCredentialSecret: nfs-credentials
  # -- Maximum number of worker threads that can concurrently run for each backup.
  backupConcurrentLimit: 2
  # -- Maximum number of volumes that can be concurrently restored on each node using a backup. When the value is "0", restoration of volumes using a backup is disabled.
  concurrentVolumeBackupRestorePerNodeLimit: 1
  # -- Setting that allows Longhorn to automatically create a default disk only on nodes with the label "node.longhorn.io/create-default-disk=true" (if no other disks exist). When this setting is disabled, Longhorn creates a default disk on each node that is added to the cluster.
  createDefaultDiskLabeledNodes: false
  # -- Percentage of storage that can be allocated relative to hard drive capacity. The default value is "100".
  storageOverProvisioningPercentage: 75
```

Then you can update the Argo CD application manifest to apply the values for your installation:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: longhorn
  namespace: argocd
spec:
  project: default
  sources:
    - chart: longhorn
      repoURL: https://charts.longhorn.io/
      targetRevision: v1.6.0 # Replace with the Longhorn version you'd like to install or upgrade to
      helm:
        valuesObject:
          preUpgradeChecker:
            jobEnabled: false
        valueFiles:
          # This path is relative to the root of the 'my-values' repo
          - '$my-values/longhorn/my-values.yaml'
    # Use a values files sourced from git
    - repoURL: 'https://<YOUR_GIT_SERVER>/<GIT_REPO>.git'
      targetRevision: master
      ref: my-values
      # You can place any additional resources (read manifests) that you would like ArgoCD to deploy/manage 
      # into this directory.
      path: longhorn/manifests
      directory:
        include: '*.yaml'
  destination:
    server: https://kubernetes.default.svc
    namespace: longhorn-system
  syncPolicy:
    automated:
      prune: true
    syncOptions:
      - CreateNamespace=true
```

## Access to the Longhorn UI

The steps below are based on the [Create an Ingress with Basic Authentication page](https://longhorn.io/docs/1.6.0/deploy/accessing-the-ui/longhorn-ingress/).

### Create secret (UI Credentials)

First we need to create a Kubernetes `secret` which will store the credentials that will be used for user authentication
(http basic-auth) when a request is sent to the Longhorn UI through the (Kubernetes) Ingress.

There are a number of ways this secret can be configured, such as creating a simple base64 encoded `basic-auth` secret, 
but to provide a better level of security we are going to use an `Opaque` secret with `htpasswd` hashed credentials.

First create the `htpasswd` password file:
```shell
htpasswd -c users longhorn-user
```
```text
New password: <password>
Re-type new password: <password>
Adding password for user longhorn-user
```
You can view the hashed password file with:
```shell
cat users
```
```text
longhorn-user:$apr1$VeRP554C$Rvj/rEsGrERPTJKeT6.R8/
```

Now we can use the `htpasswd` file to create the contents of the secret:

```shell
kubectl create secret generic longhorn-ui-basic-auth \
-n longhorn-system --from-file=users
```

You can view the contents of the secret with:

```shell
kubectl get -n longhorn-system secret longhorn-ui-basic-auth -o jsonpath='{.data}'
```
```text
{"users":"bG9uZ2hvcm4tdXNlcjokYXByMSRWZVJQNTU0QyRSdmovckVzR3JFUlBUSktlVDYuUjgvCg=="}
```

Note that the users value is **base64 encoded**, so to see the decoded value you can use something like:

```shell
kubectl get -n longhorn-system secret longhorn-ui-basic-auth -o jsonpath='{.data.users}' | base64 --decode
```
```text
longhorn-user:$apr1$VeRP554C$Rvj/rEsGrERPTJKeT6.R8/
```

### Create Traefik Middleware

We need to create a Traefik middeware, which will be referenced in the Ingress, to instruct [Traefik to perform BasicAuth](https://doc.traefik.io/traefik/middlewares/http/basicauth/) 
on requests to our Longhorn UI.

```yaml
apiVersion: traefik.containo.us/v1alpha1
kind: Middleware
metadata:
  name: longhorn-auth
  namespace: longhorn-system
spec:
  basicAuth:
    # References the name of the secret containing the longhorn UI credentials
    secret: longhorn-ui-basic-auth
```

> You can deploy this manually or better yet get Argo CD to manage this by placing the file in the 
> `longhorn/manifests` directory of your git repo which is referenced in your Argo CD application manifest (see above).

### Create Ingress

You can get the helm chart to create the Ingress object for you by adding the following to your `my-values.yaml`:

```yaml
ingress:
  # Enable ingress generation
  enabled: true
  host: "longhorn.example.com" # Change to your DNS name
  tls: true
  tlsSecret : longhorn-tls # The name of the secret which contains (or will contain) the longhorn certificate
  annotations: {
    # Annotation specifying the cert-manager issuer to use.
    cert-manager.io/cluster-issuer: <NAME_OF_CLUSTER_ISSUER_TO_USE>,
    # Add authorisation to ingress endpoint by referencing the Traefik middleware
    traefik.ingress.kubernetes.io/router.middlewares: longhorn-system-longhorn-auth@kubernetescrd
  }
```

### Viewing the Longhorn UI

With the configuration above you should be able to reach the longhorn UI at the url you used in your `my-values.yaml` 
(i.e. https://longhorn.example.com but your DNS value).

> To view the longhorn UI with a browser you will need to set up; DNS records to direct requests to your Kubernetes 
> cluster, or modify your systems `hosts` file to do the same.

You can use `curl` to see if you can reach the longhorn UI with something like:

```shell
curl https://longhorn.example.com/ \
-u 'longhorn-user:password' 
```

## Monitoring integration with Promethues

In this section we are going to configure the [existing kube-prometheus setup](2024-01-03-Prometheus-on-k3s) 
of the cluster to also scrape longhorn metrics.

This section is based on the information on the [Setting up Prometheus and Grafana to monitor Longhorn](https://longhorn.io/docs/1.6.0/monitoring/prometheus-and-grafana-setup/)
page.

### Enable creation of Prometheus ServiceMonitor

We can get the helm chart to create the Prometheus `ServiceMonitor` resource for us by adding the following block to our 
`my-values.yaml` file:

```yaml
metrics:
  serviceMonitor:
    # Setting that allows the creation of a [Prometheus Operator](https://prometheus-operator.dev/) ServiceMonitor 
    # resource for Longhorn Manager components.
    enabled: true
```

### Prometheus Role and RoleBinding

By default, _kube-prometheus_ only watches the `monitoring`, `kube-system` and `default` namespaces. To get it to automatically 
discover and scrape ServiceMonitors in other namespaces (like the `longhorn-system` namespace) we need to give it permissions
by deploying a `Role` and `RoleBinding`.

> You can deploy the `Role` and `RoleBinding` manually or better yet get Argo CD to manage/deploy them by placing the 
> files in the `longhorn/manifests` directory of your git repo which is referenced in the Argo CD application manifest 
> (see above).

Once the `Role` and `RoleBinding` have been deployed you should see the `serviceMonitor/longhorn-system/longhorn-prometheus-servicemonitor`
target(s) turn up in Prometheus.

#### Role

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

#### RoleBinding

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

### Adding the Grafana Dashboard

Longhorn have kindly published a [Grafana dashboard](https://grafana.com/grafana/dashboards/17626-longhorn-example-v1-4-0/) 
which displays many of the metrics scrapped from the longhorn ServiceMonitor.

You can add this to the Grafana instance of your `kube-promethues` stack through the Grafana UI.

In the Grafana UI go to Bashboards --> New --> Import

In the _Import via grafana.com_ field enter the ID of the [Longhorn Example v1.4.0 Grafana Dashboard](https://grafana.com/grafana/dashboards/17626-longhorn-example-v1-4-0/)
(which is `17626`), press _Load_.

On the Import dashboard screen select the _prometheus_ data source and then press _Import_.

**Done!** You should now see the Longhorn Example v1.4.0 dashboard.

## Links & References

* [Install with ArgoCD - longhorn](https://longhorn.io/docs/1.6.0/deploy/install/install-with-argocd/)
* [Helm values - longhorn](https://longhorn.io/docs/1.6.0/references/helm-values/)
* [Create an Ingress with Basic Authentication - longhorn](https://longhorn.io/docs/1.6.0/deploy/accessing-the-ui/longhorn-ingress/)
* [Managing Secrets using kubectl - Kubernetes](https://kubernetes.io/docs/tasks/configmap-secret/managing-secret-using-kubectl/)
* [Traefik BasicAuth documentation](https://doc.traefik.io/traefik/middlewares/http/basicauth/)
* [Setting up Prometheus and Grafana to monitor Longhorn](https://longhorn.io/docs/1.6.0/monitoring/prometheus-and-grafana-setup/)
* [Longhorn Example v1.4.0 - Grafana Dashboard](https://grafana.com/grafana/dashboards/17626-longhorn-example-v1-4-0/)