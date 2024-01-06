---
title: "Argo CD on K3s"
date: 2023-12-31T08:30:09+11:00
draft: false
tags:
- kubernetes
- development
- k3s
- ci_cd
- argo_cd
---

This post covers the installation and configuration of [Argo CD](../tools/kubernetes/argo_cd.md) within my [k3s](../tools/kubernetes/k3s) cluster. 
<!--more-->
My intent is to use Argo CD to be able to automatically update the Kubernetes objects deployed within my k3s cluster 
when I make changes to the source code within git.

## Prerequisites

* A Kubernetes cluster - I used an existing k3s cluster
* kubectl - CLI tool
* A kubeconfig for your cluster - default location is `~/.kube/config`

## Install Argo CD

Create the `argocd` namespace and apply the Argo CD manifest:

```shell
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## Accessing Argo CD Server

The simplest way to get to the Argo CD UI use port-forwarding with ``kubectl port-forward svc/argocd-server -n argocd 8080:443``.
The UI can then be accessed using https://localhost:8080

The steps below walk through setting up a Kubernetes `ingress` object so that Traefik provides the TLS termination into 
the cluster.

By default, the Argo CD server does its own TLS termination using a self-signed certificate. To use an alternate TLS termination
provider we need to disable Argo CD's TLS otherwise we will encounter (Too many redirect) issues.

The spec of the `argocd-server` Deployment uses the `ARGOCD_SERVER_INSECURE` environment variable which is sourced from 
the `argocd-cmd-params-cm` ConfigMap if set.

```yaml
# Snippet from the `argocd-server` Deployment manifest
          env:
            - name: ARGOCD_SERVER_INSECURE
              valueFrom:
                configMapKeyRef:
                  key: server.insecure
                  name: argocd-cmd-params-cm
                  optional: true
```

You can disable TLS termination for the Argo CD server by applying an updated `argocd-cmd-params-cm` ConfigMap. 
Create a yaml file (`argocd-cmd-params-cm.yaml`) containing the content below: 

```yaml
#
# Custom Argo CD config to:
#  - Disable TLS termination on the Argo CD server
#
# Apply with the commands:
#   kubectl.exe apply -f .\argocd-cmd-params-cm.yaml
#   kubectl --namespace argocd rollout restart deployment argocd-server
#
# For the list of flags that can be used see: https://argo-cd.readthedocs.io/en/stable/operator-manual/argocd-cmd-params-cm.yaml
#
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-cmd-params-cm
  namespace: argocd
  labels:
    app.kubernetes.io/name: argocd-cmd-params-cm
    app.kubernetes.io/part-of: argocd
data:
  # Use an unsecure connection as TLS termination is handled by Traefik
  server.insecure: "true"
```

And apply it to your cluster with:

```shell
kubectl apply -f .\argocd-cmd-params-cm.yaml
```

Restart the `argocd` deployment so this change takes effect:

```shell
kubectl --namespace argocd rollout restart deployment argocd-server
```

When using Traefik as an Ingress Controller there are **two options** for enabling access to the Argo CD:

1. Create a Traefik `IngressRoute` object - Requires the Traefik CRDs to be applied, supports both the UI and Argo CD CLI tool.
2. Create a Kubernetes `Ingress` object - Simple, but does not support access from the Argo CD CLI

Both of these options are outlined below.

> Note: To reach the Argo CD server easily you will need to either add a mapping for your `<ARGO_CD_DNS_NAME>` (to an IP 
> address of a node in the cluster) to your machines `hosts` file or update your DNS entries to do the same.

### Option 1 (Preferred) - Traefik `IngressRoute` object

This option is based on the information in the [Argo CD Traefik Ingress Configuration documentation page](https://argo-cd.readthedocs.io/en/stable/operator-manual/ingress/#traefik-v22).

To create a Traefik `IngressRoute` which will be used by Traefik to provide TLS termination
to the `<ARGO_CD_DNS_NAME>` URL, create a file (`argocd-ingressroute.yaml`) with the following content:

```yaml
#
# Description: Traefik IngressRoute to allow (UI and API) access to the Argo CD server.
#               Requires the Traefik Kubernetes CRDs to be installed.
#
# Apply with:
#   kubectl apply -f .\argocd-ingressroute.yaml
#
---
apiVersion: traefik.containo.us/v1alpha1
kind: IngressRoute
metadata:
  name: argocd-server
  namespace: argocd
spec:
  entryPoints:
    - websecure
  routes:
    - kind: Rule
      match: Host(`<ARGO_CD_DNS_NAME>`)
      priority: 10
      services:
        - name: argocd-server
          port: 80
    - kind: Rule
      match: Host(`<ARGO_CD_DNS_NAME>`) && Headers(`Content-Type`, `application/grpc`)
      priority: 11
      services:
        - name: argocd-server
          port: 80
          scheme: h2c
  tls:
    # Update to match the name of the certificate resolver in your Traefik instance you want to use
    certResolver: default
```

You should now be able to reach the Argo CD UI at: [https://<YOUR_ARGO_CD_DNS_NAME>]() and be able to use the Argo CD CLI
for example: `argocd version --server argocd.my-space.duckdns.org`

### Option 2 - Kubernetes `Ingress` object

To create an `Ingress` which will be used by Traefik (default k3s Ingress Controller) to provide TLS termination 
to the `<ARGO_CD_DNS_NAME>` URL, create a file (`argocd-ingress.yaml`) with the following content:

```yaml
#
# Ingress for access to Argo CD UI
#
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: argocd-ui
  namespace: argocd
  labels:
    app.kubernetes.io/part-of: argocd
    app.kubernetes.io/name: argocd-server
  annotations:
    traefik.ingress.kubernetes.io/router.entrypoints: websecure
    traefik.ingress.kubernetes.io/router.tls.certresolver: letsencrypt--duckdns-resolver
spec:
  rules:
    - host: "<ARGO_CD_DNS_NAME>"
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: argocd-server
                port:
                  number: 80
```

And apply to the cluster with:

```shell
kubectl apply -f .\argocd-ingress.yaml
```

You should now be able to reach the Argo CD UI at: `https://<YOUR_ARGO_CD_DNS_NAME>`

## Login

The initial password for the `admin` account is auto-generated and stored as clear text in the field `password` in a 
secret named `argocd-initial-admin-secret` in your Argo CD installation namespace.

You can retrieve the base64 encoded value with:

```shell
kubectl.exe -n argocd get secret argocd-initial-admin-secret -o yaml
```
```text
apiVersion: v1
kind: Secret
data:
  password: BASE64_ENCODED_PASSWORD_STRING
metadata:
  name: argocd-initial-admin-secret
  namespace: argocd
...
```

To use this value to log in to the UI (or Argo CD CLI) you will need to decode the base64 string to its original value. 
For example in [powershell](../languages/powershell) use:

```powershell
# Convert Base64 encoded password to plain text
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("BASE64_ENCODED_PASSWORD_STRING"))
```

> You should delete the `argocd-initial-admin-secret` from the Argo CD namespace once you changed the password. 
> The secret serves no other purpose than to store the initially generated password in clear and can safely be deleted at any time. 
> To delete run ``kubectl -n argocd delete secret argocd-initial-admin-secret``

## Register a (Helm) application from a self-hosted git repo (gogs)

The steps below outline the process of registering a new Argo CD application using the Helm chart source code and values 
file stored in a self-hosted git ([gogs](../tools/gogs)) repository.

> Note: Version `v2.9.3+6eba5be` of the Argo CD UI encounters an issue when creating a new application with a 
> self-hosted git (gogs) repositories which prevents the 
> The workaround is to either use the Argo CD CLI or create (and apply) the `argoproj` Application manifests manually,
> which is what have done below.

The Kubernetes/Argo CD application that I am using is based on ([Dashy](https://github.com/lissy93/dashy)) personal dashboard app.
My git repository contains the source code for a Helm chart (and values files) which uses the Dashy docker image.

The structure of the git repository is as follows: 

```text
├── dashy
│   ├── charts
│   ├── templates
│   │   ├── tests
│   │   │   ├── test-connection.yaml
│   │   ├── _helpers.tpl
│   │   ├── configmap.yaml
│   │   ├── deployment.yaml
│   │   ├── hpa.yaml
│   │   ├── ingress.yaml
│   │   ├── NOTES.txt
│   │   ├── service.yaml
│   │   ├── serviceaccount.yaml
│   ├── Chart.yaml
│   ├── values.yaml
│   ├── .helmignore
├── my-values.yaml 
├── README.md
└── .gitignore
```

The important thing to note is that the `my-values.yaml` file is a sibling to the `dashy` directory which contains the 
source code for the helm chart.

To create this Arcgo CD application manually we create a `dashy-app.yaml` manifest file with the following contents: 

```yaml
#
# Description: This is the Argo CD manifest for use of the Dashy application (helm chart) 
#
# To apply run:
#   kubectl.exe apply -f .\dashy-app.yaml
#
---
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  # The name needs to be lower case
  name: dashy
  namespace: argocd
spec:
  destination:
    namespace: default
    # Refers to the Kubernetes cluster where Argo CD is running
    server: 'https://kubernetes.default.svc'
  source:
    # The path/directory containing the helm chart source code
    path: dashy
    repoURL: 'https://<GIT_REPO_HOST>/<PROJECT>/dashy.git'
    # The git branch to use as the source of the Kubernetes app
    targetRevision: main
    helm:
      # The value files to apply to the Helm chart when deploying
      # Note: these files are relative to the 'path' directory set above
      valueFiles:
      - "../my-values.yaml"
  project: default
```

This can then be applied using the command:

```shell
kubectl.exe apply -f .\dashy-app.yaml
```

The Argo CD UI should now show the ``dashy`` app with an **un-synced** state and .

From here you can deploy the application to the cluster from the UI using the **_SYNC_** button and enable **_AUTO-SYNC_**.

_AUTO-SYNC_ will poll the git repository periodically (every 3 minutes by default) and if there are any changes (i.e. a new 
commit) will apply these changes to the cluster.

Alternatively you can set up webhook in your git repository to notify Argo CD of any changes, which will trigger an 
update (SYNC) of the application. This is explained further below. 

## (Optional) Git repository webhook

You can set up webhook in your git repository to notify Argo CD of any changes (commits). Argo CD will then _SYNC_ the 
application, applying the latest version of the source code to the Kubernetes cluster.

This can be a real time saver as it removes the need to manually apply/deploy the changes to the cluster and reduces the
potential for human error.

The information below was based on the [Argo CD Git Webhook Configuration documentation](https://argo-cd.readthedocs.io/en/stable/operator-manual/webhook/).

In my setup I am using a self-hosted [gogs](../tools/gogs) instance as my git repo server.

To set up the Argo CD webhook for a particular repository:  
Settings --> Webhooks --> Add a new webhook (Gogs)

* Payload URL:`https://<ARGO_CD_DNS_NAME>/api/webhook`
* Content Type: `application/json`
* Secret: <LEAVE_EMPTY>
* When should this webhook be triggered?: `Just the push event`

> Note: Depending on your gogs configuration you may need to add an Argo CD entry to your ``LOCAL_NETWORK_ALLOWLIST``.  
> For example: `LOCAL_NETWORK_ALLOWLIST = <ARGO_CD_DNS_NAME>`

Once created you can test the trigger by making some changes to the repo, committing them, and pushing them back to origin.
This should trigger a SYNC of the Argo CD application.

## Done!

This brings us to the end of this post and the basic setup of Argo CD on a k3s cluster.

## References

Below are the links to some of the references used for this post:

* [Getting Started - Argo CD](https://argo-cd.readthedocs.io/en/stable/getting_started/)
* [Argo CD Install Manifests](https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml)
* [Traefik Ingress Configuration - Argo CD](https://argo-cd.readthedocs.io/en/stable/operator-manual/ingress/#option-2-ssl-termination-at-ingress-controller)
* [Additional configuration method - Argo CD](https://argo-cd.readthedocs.io/en/stable/operator-manual/server-commands/additional-configuration-method/)
* [Traefik Ingress Configuration - Argo CD](https://argo-cd.readthedocs.io/en/stable/operator-manual/ingress/#traefik-v22)
* [Can ArgoCD deploy a Helm chart from a Git repository, not from Helm chart repository - stackoverflow](https://stackoverflow.com/questions/76905630/can-argocd-deploy-a-helm-chart-from-git-repository-not-from-helm-chart-reposito)
* [Git Webhook Configuration - Argo CD](https://argo-cd.readthedocs.io/en/stable/operator-manual/webhook/).