---
title: Traefik
tags:
 - container
 - kubernetes
 - docker
 - development
 - certificate
---

Traefik is a modern HTTP reverse proxy and load balancer that makes deploying microservices easy.
<!--more-->
Traefik integrates with your existing infrastructure components (Docker, Swarm mode, Kubernetes, Marathon, Consul, Etcd, Rancher, Amazon ECS, ...) and configures itself automatically and dynamically.

## [Configuration](https://doc.traefik.io/traefik/v2.0/getting-started/configuration-overview/)

There are three different, mutually exclusive (e.g. you can use only one at a time), ways to define static configuration options in Traefik:

1. In a configuration file
2. In the command-line arguments
3. As environment variables
These ways are evaluated in the order listed above.

## Kubernetes resource annotations

When deployed as the Ingress controller in a Kubernetes cluster Traefik will watch for [specific annotations](https://doc.traefik.io/traefik/routing/providers/kubernetes-ingress/#annotations) 
on Kubernetes Ingress and Service resources and use these to configure its routes.

Below is an example of the use of these annotations on an Ingress resource:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    # Refers to the name of a specific Traefik entrypoint set in the Traefik configuration
    traefik.ingress.kubernetes.io/router.entrypoints: websecure
    # Refers to the name of a specific Traefik certificate resolver set in the Traefik configuration
    traefik.ingress.kubernetes.io/router.tls.certresolver: letsencrypt--duckdns-resolver
  name: app
  namespace: default
spec:
  ingressClassName: traefik
  rules:
    - host: app.example.org
      http:
        paths:
          - backend:
              service:
                name: backend
                port:
                  number: 3000
            path: /
            pathType: Prefix
```

### Handy Ingress annotations

Lists the name(s) of the specific Traefik entrypoints as per the Traefik configuration

```yaml
traefik.ingress.kubernetes.io/router.entrypoints: ep1,ep2
```

If certResolver is defined, Traefik will try to generate certificates based on routers Host & HostSNI rules.
```yaml
traefik.ingress.kubernetes.io/router.tls.certresolver: myresolver
```

## Custom Resource Definition (CRD)

Traefik can use [Custom Resource Definitions (CRDs)](https://doc.traefik.io/traefik/routing/providers/kubernetes-crd/) to 
configure the routing it performs within a Kubernetes cluster.

### IngressRoute

IngressRoute is the CRD implementation of a [Traefik HTTP router](https://doc.traefik.io/traefik/routing/routers/#configuring-http-routers).

