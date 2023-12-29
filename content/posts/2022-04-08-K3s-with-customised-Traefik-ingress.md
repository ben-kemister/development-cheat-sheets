---
title: "K3s with customised Traefik Ingress"
draft: false
tags:
    - kubernetes
    - ingress
    - traefik
    - linux
    - k3s
    - project
---

This post contains some details on how to customise the k3s (default) traefik ingress controller.
<!--more-->
K3s comes with a pre-configured [traefik ingress controller](https://doc.traefik.io/traefik/routing/providers/kubernetes-ingress/) which is deployed as a part of the k3s installation/setup.
By default, traefik *listens* for changes in the kubernetes *services* and *ingresses* and will use the information, along with any annotations, to configure itself accordingly.

To add/alter this default configuration you need to use *apply* a **HelmChartConfig** Custom Resource Definition (CRD). 
For more information see the [Customizing Packaged Components with HelmChartConfig K3s page](https://rancher.com/docs/k3s/latest/en/helm/#customizing-packaged-components-with-helmchartconfig).

## Applying your HelmChartConfig

To apply your custom *HelmChartConfig*, which is just a yaml file, use the following command:

```sh
sudo k3s kubectl apply -f yourHelmChartConfig.yaml
```

This will cause the K3s cluster to *rebuild* the details of the traefik deployment, then update the cluster with the new version of the traefik deployment, which includes your customisations.

## My desired customisations

There were a number of things that I wanted to customise for my Traefik instance which included:

1. Using a Letscrypt certificate (via acme and duckdns)
   1. I wanted to persist these between pods/containers so that I did not risk hitting any [rate limits](https://letsencrypt.org/docs/rate-limits/).
2. Setup extra entry points/posts so that I easily reach services inside my k3s cluster
3. Be able to access the Traefik dashboard
4. others...

## Setup K3s bash aliases

If you haven't done so already setting up some aliases will make life easier and save on keystrokes for common commands.

```sh
ssh <user>@<server>

# Create edit the .bash_aliases file
nano ~/.bash_aliases

# add the following line 
alias k='sudo k3s kubectl'
alias kga='sudo k3s kubectl get -A'
# Write out the file (Ctrl + O), and exit (Ctrl + X)

# Load the aliases file, so that you don't need to log out of the ssh session.
. ~/.bash_aliases
```

## Letscrypt Certificate Storage

I had quite a number of issues trying to get Traefik to store the Letscrypt certificates. Originally I wanted these to be stored on my NAS and accessed via NFS with a kubernetes persistent volume. Unfortunately Traefik requires that the `acme.json` has file permissions of 600, which I was not able to achieve with my NAS-NFS-PV setup.

In the end I used a directory on the k3s server and setup a Persistent Volume (PV) which used `nodeAffinity` to make sure that it was always hosted on the same node.

### StorageClass

The [local section of the kubernetes volume doco](https://kubernetes.io/docs/concepts/storage/volumes/#local) advised setting up a new [StorageClass](https://kubernetes.io/docs/concepts/storage/storage-classes/#local) for this.

```yaml
# A custom StorageClass for use with local (node) persistent storage
---
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: local-persistent-storage
provisioner: kubernetes.io/no-provisioner
reclaimPolicy: Retain
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
```

### Create local directory

On my server I created a new directory to house my certificates:

```sh
sudo mkdir -p /var/local/k3s/traefik
```

### PersistentVolume

Then I setup a `PersistentVolume` volume to access this local directory using `nodeAffinity` to make sure that it was always bound to the right node.

```yaml
# For the persistent storage of Traefik data (certs etc.) on the local disc of the pi-server node
#
# Note: Due to the file permission restrictions around the acme.json (letsencrypt certs) file
#       I was not able to get Traefik to use the NAS via an NFS PV.
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: traefik-local
spec:
  capacity:
    storage: 100Mi
  # If not set defaults to Filesystem
  volumeMode: Filesystem
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  storageClassName: local-persistent-storage
  local:
    # Note: this directory needs to exist on the node selected from the expression below
    path: /var/local/k3s/traefik
  nodeAffinity:
    required:
      nodeSelectorTerms:
        - matchExpressions:
            - key: kubernetes.io/hostname
              operator: In
              values:
                - pi-server
```

### PersistentVolumeClaim

Next I created a `PersistentVolumeClaim` which would bind the Traefik volume in the deployment/pod to the PV.

```yaml
# Needed to support the storage of Traefik data such as letsencrypt certs
#
# Note: as the 'local-persistent-storage' has volumeBindingMode: WaitForFirstConsumer
# This PVC will not bind until a container starts to use it.
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: traefik-local
  namespace: kube-system
spec:
  # Must match the storageClassName in the PersistentVolume
  storageClassName: local-persistent-storage
  # The access modes must match those in a PersistentVolume for it to get Bound
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 100Mi
```

## HelmChartConfig

Now the persistence has been setup you can now refer to them in your `HelmChartConfig` which contains all of the extra configuration items that you want for your Traefik instance.

```yaml
# This configures the built in k3s traefik instance with my additional bits
---
apiVersion: helm.cattle.io/v1
kind: HelmChartConfig
metadata:
  name: traefik
  namespace: kube-system
spec:
  valuesContent: |-
    
    # Configure Traefik static configuration
    # Additional arguments to be passed at Traefik's binary
    # All available options available on https://docs.traefik.io/reference/static-configuration/cli/
    additionalArguments:
      - "--api.insecure=true"
      #- "--api.dashboard=true"
      # This is the letsencrypt staging server, use this in testing to avoid rate limits
      - "--certificatesresolvers.letsencrypt--duckdns-resolver.acme.caServer=https://acme-staging-v02.api.letsencrypt.org/directory"
      - "--certificatesresolvers.letsencrypt--duckdns-resolver.acme.dnsChallenge=true"
      - "--certificatesresolvers.letsencrypt--duckdns-resolver.acme.dnsChallenge.provider=duckdns"
      - "--certificatesresolvers.letsencrypt--duckdns-resolver.acme.email=<YOUR_EMAIL_ADDRESS>"
      - "--certificatesresolvers.letsencrypt--duckdns-resolver.acme.storage=/data/letsencrypt/acme.json"
    
    # Environment variables to be passed to Traefik's binary
    # Ideally this should be done via kubernetes secrets!
    env:
      - name: DUCKDNS_TOKEN
        value: "<YOUR_DUCK_DNS_TOKEN>"
    
    image:
      name: traefik
      tag: v2.7
    
    logs:
      general:
        level: DEBUG
    
    ports:
      oracle-db:
        port: 1521
        expose: true
        exposedPort: 1521
        protocol: TCP
      image-pull:
        port: 5000
        expose: true
        exposedPort: 5000
        protocol: TCP
      image-push:
        port: 5001
        expose: true
        exposedPort: 5001
        protocol: TCP
      
    persistence:
      enabled: true
      existingClaim: traefik-local
```

After you apply this to your k3s cluster `sudo k3s kubectl apply -f HelmChartConfig.yaml`, the cluster will re-create the Traefik deployment and start Traefik with the new updated configuration.

## (optional) Exposing the Traefik dashboard

Exposing the Traefik dashboard gives you a user friendly way to visualize what is going on in the Traefik system.

### Dashboard Service

First create a `Service` to expose the ports from the Traefik pod/instance.

```yaml
# Exposes the traefik (admin) web ui to the cluster
---
kind: Service
apiVersion: v1
metadata:
  name: traefik-dashboard
  namespace: kube-system
  labels:
    app.kubernetes.io/instance: traefik
    app.kubernetes.io/name: traefik-dashboard
spec:
    type: NodePort
    ports:
      - name: traefik
        port: 9000
        targetPort: 9000
        protocol: TCP
    selector:
      # These need to match the labels of the k3s traefik instance
      app.kubernetes.io/instance: traefik
      app.kubernetes.io/name: traefik
```

### Dashboard Ingress

Lastly create an `Ingress` to expose the traefik dashboard.

```yaml
# Exposes the Traefik dashboard
---
kind: Ingress
apiVersion: networking.k8s.io/v1
metadata:
  name: traefik-dashboard
  namespace: kube-system
  annotations:
    traefik.ingress.kubernetes.io/router.entrypoints: web
spec:
  rules:
    - host: "<YOUR_DNS_ENTRY>"
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: traefik-dashboard
                port:
                  name: traefik
```

## Handy Commands

```sh
# Display the dashboard service
k describe -n kube-system svc traefik-dashboard
```

## Links and Further information

* [Github Issue - Updating K3s Traefik Ingress Configuration](https://github.com/k3s-io/k3s/issues/1313#issuecomment-680997505)
* [The values and schema used in the Traefik helm chart](https://github.com/traefik/traefik-helm-chart/blob/master/traefik/values.yaml#L170-L171)
* [How to expose the Traefik dashboard in k3s](https://k3s.rocks/traefik-dashboard/)
* <https://medium.com/kubernetes-tutorials/deploying-traefik-as-ingress-controller-for-your-kubernetes-cluster-b03a0672ae0c>
