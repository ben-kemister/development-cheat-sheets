---
title: "Cert-Manager with Let's Encrypt and DuckDNS on K3s"
date: 2024-01-10T09:11:27+11:00
draft: false
tags:
- kubernetes
- k3s
- cert-manager
- certificate
- lets_encrypt
- argo_cd
---

This post is about the installation and configuration of [cert-manager](../tools/kubernetes/cert-manager) on my [k3s](../tools/kubernetes/k3s) cluster.
<!--more-->

I originally started off using the ACME capabilities of the default/native [Traefik](../tools/traefik) instance of 
[my customised configuration](./2022-04-08-K3s-with-customised-Traefik-ingress.md). 
But as the number of apps running in my cluster grew I started to reach the limits of this setup, namely issues with 
creating and renewing [Let's Encrypt](https://letsencrypt.org/) certificates.

The aim of this post is to use cert-manager to manage my Let's Encrypt certificates with Traefik and hopefully simplify
(and automate) the process.

## Install cert-manager

You can install cert-manager a number of ways (kubeclt apply, Helm, CD, etc), for this post I am going to use [Argo CD](../tools/kubernetes/argo_cd) 
to apply the [cert-manager Helm chart](https://artifacthub.io/packages/helm/cert-manager/cert-manager), 
using a **custom values file** sourced from a **private git repository**.

### Custom values file

There are a few values that need to set when installing/deploying on our cert-manager Helm chart, we will use an 
external values files to store and apply these.

Create a `my-values.yaml` file within your private git repo with the following contents:

```yaml
#
# Description: This file contains my values to use when applying the cert-manager helm chart
#
# Apply with:
#   helm install --dry-run test-cert-manager jetstack/cert-manager -f ./my-values.yaml --namespace cert-manager --create-namespace --version v1.13.3
#
---

# Install the cert-manager CRDs
installCRDs: true

extraArgs:
  # Only use the provided name servers rather than the defaults of the container
  - --dns01-recursive-nameservers-only
  #  Use the Cloudflare DNS server (1.1.1.1:53) or the Google DNS server (8.8.8.8:53)
  - --dns01-recursive-nameservers=1.1.1.1:53,8.8.8.8:53
```

### Create Argo CD application

To get Argo CD to deploy the cert-manager manifests (with our custom values) we need to create an Argo CD application resource.

Create the Argo CD application by creating a `argocd-cert-manager-app.yaml` file with the following contents:

```yaml
#
# Description: This is the Argo CD manifest for the cert-manager application (helm chart)
#
# To apply run:
#   kubectl.exe apply -f .\argocd-cert-manager-app.yaml
#
---
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: cart-manager
  namespace: argocd
spec:
  destination:
    namespace: cert-manager
    server: 'https://kubernetes.default.svc'
  sources:
    - repoURL: 'https://charts.jetstack.io'
      chart: cert-manager
      targetRevision: v1.13.3
      helm:
        valueFiles:
          # This path is relative to the root of the 'my-values' repo
          - '$my-values/cert-manager/my-values.yaml'
    # Retrieve the custom values file from a different git repo
    - repoURL: '<GIT_REPO_WITH_VALUES_FILE>'
      targetRevision: master
      ref: my-values
  syncPolicy:
    # Automatically detect changes and deploy them to the cluster
    automated:
      # Remove kubernetes objects/resources that are no longer present
      prune: true
    syncOptions:
      # Automatically create the `cert-manager` namespace if it doesn't exist
      - CreateNamespace=true
  project: default
```

Then apply this to your cluster using:

```shell
kubectl.exe apply -f .\argocd-cert-manager-app.yaml
```

## Install duck-dns webhook

Unfortunately cert-manager does not have in-tree support for a ACME DNS01 challenger solver for [duckDNS](https://www.duckdns.org/).
Luckily cert-manager supports out-of-tree ACME solvers via a [webhook](https://cert-manager.io/docs/configuration/acme/dns01/webhook/), 
and [cert-manager-webhook-duckdns](https://github.com/ebrianne/cert-manager-webhook-duckdns) is one implementation specifically built for dealing with DuckDNS.
Internally it uses the DuckDNS API to support ACME DNS01 challenges.

The [cert-manager-webhook-duckdns repo](https://github.com/ebrianne/cert-manager-webhook-duckdns) includes a Helm chart 
and there is a published image available on [Docker Hub](https://hub.docker.com/r/ebrianne/cert-manager-webhook-duckdns).

We can add the `cert-manager-webhook-duckdns` helm chart to our Argo CD cert-manager Application manifest (along with a 
custom values file for the `cert-manager-webhook-duckdns` helm chart) so that they are all deployed and managed together.

### Create custom values file

We will use a custom values files to store our `cert-manager-webhook-duckdns` helm chart values in a separate git 
repository.

Create a `duckdns-webhook/my-webhook-values.yaml` file within your private git repo with the following contents:

```yaml
#
# Description: This file contains the values to use when applying the cert-manager-webhook-duckdns helm chart
#
# See: https://github.com/ebrianne/cert-manager-webhook-duckdns?tab=readme-ov-file
#
---

image:
  # Target a particular image to keep things nice and stable
  tag: v1.2.3

duckdns:
  token: "<YOUR_DUCK_DNS_TOKEN>"

logLevel: 2

clusterIssuer:
  email: <YOUR_DUCK_DNS_EMAIL>
  production:
    # Creates a ClusterIssuer called 'cert-manager-cert-manager-webhook-duckdns-production'
    create: true
  staging:
    # Creates a ClusterIssuer called 'cert-manager-cert-manager-webhook-duckdns-staging`
    create: true
```

Be sure to update the `duckdns.token` and `clusterIssuer.email` fields with the values from your DuckDNS account.

### Create Argo CD application

Now we can update the `argocd-cert-manager-app.yaml` file to add the `cert-manager-webhook-duckdns` helm chart:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: cert-manager
  namespace: argocd
spec:
  #...
  sources:
    ...
    # Duck DNS webhook
    - repoURL: 'https://github.com/ebrianne/cert-manager-webhook-duckdns.git'
      path: deploy/cert-manager-webhook-duckdns
      targetRevision: v1.2.3
      helm:
        valueFiles:
          # This path is relative to the root of the 'my-values' repo and references the duckdns-webhook values file
          - '$my-values/cert-manager/duckdns-webhook/my-webhook-values.yaml'
  #...
```

Apply the updated Argo CD manifest to the cluster with:

```shell
kubectl.exe apply -f .\argocd-cert-manager-app.yaml
```

## Generating and Using a certificate

You can use annotations on your Ingress resources to get cert-manager to generate/manage the Certificate and Secret resources.

The Certificate resource contains metadata about the certificate such as the resource owner(s), reference to the issuer,
reference to the secret, the certificate dates, and when it should be renewed.

The actual certificate issued (by Let's Encrypt in this case) is stored in the Secret which is used by the Ingress Controller
(Traefik) to secure the Ingress.

Below is a simple Ingress which uses the `cert-manager-cert-manager-webhook-duckdns-staging` ClusterIssuer 
(i.e. Let's Encrypt staging server) to complete the DNS01 challenge and issue certificate which is used when the cluster 
is communicating with request to `https://whoami.<YOUR_SUB_DOMAIN>.duckdns.org`

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    # This annotation directs cert-manager to use the 'cert-manager-cert-manager-webhook-duckdns-staging' ClusterIssuer
    cert-manager.io/cluster-issuer: cert-manager-cert-manager-webhook-duckdns-staging
  name: whoami
  namespace: default
spec:
  rules:
  - host: whoami.<YOUR_SUB_DOMAIN>.duckdns.org
    http:
      paths:
      - backend:
          service:
            name: whoami
            port:
              number: 80
        path: /
        pathType: Prefix
  tls:
  - hosts:
    # Needs to match the host in the spec.rules section above
    - whoami.<YOUR_SUB_DOMAIN>.duckdns.org
    # The name of the Certificate and Secret resources created/managed by cert-manager
    secretName: whoami-tls
```

## (Optional) Restart Traefik svclb pods

> This part is only required if your https endpoints are returning old/incorrect certificates.

Before starting this exercise I used the ACME capabilities of the native Traefik instance within k3s to manage my ACME/DuckDNS 
configuration to manage certificates using Traefik specific annotations on my [Ingress resources](https://doc.traefik.io/traefik/routing/providers/kubernetes-ingress/#on-ingress).

While installing and configuring cert-manager in my cluster I came across a situation where some of the Traefik `svclb-traefik` 
pods return the old certificates, rather than the latest ones created by cert-manager.

To resolve this issue I simply deleted the `svclb-traefik` pods, which were then re-created and used the cert-manager 
certificates as expected.

To restart the pods use this funky [PowerShell](../languages/powershell) command:

```powershell
# Finds the svclb-traefik pods and deletes them. They will be recreated by Kubernetes
kubectl get -n kube-system pods -o custom-columns=NAME:metadata.name | Select-String '^svclb-traefik-' | foreach { kubectl delete -n kube-system pod $_ }
```

## References

Below are the links to some of the references used for this post:

* [Getting Started - Argo CD](https://argo-cd.readthedocs.io/en/stable/getting_started/)
* [Helm Installation - cert-manager](https://cert-manager.io/docs/installation/helm/)
* [cert-manager-webhook-duckdns - GitHub](https://github.com/ebrianne/cert-manager-webhook-duckdns?tab=readme-ov-file)
* [cert-manager-webhook-duckdns - Docker Hub](https://hub.docker.com/r/ebrianne/cert-manager-webhook-duckdns)
* [Traefik & Kubernetes Annotations - Traefik](https://doc.traefik.io/traefik/routing/providers/kubernetes-ingress/#annotations)
* [kubectl get filter pods by partial name - StackOverflow](https://stackoverflow.com/questions/70282381/kubectl-get-pods-how-to-filter-pods-by-partial-name)