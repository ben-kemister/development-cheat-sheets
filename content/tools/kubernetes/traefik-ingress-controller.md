---
title: Traefik (Ingress Controller)
tags:
- kubernetes
- certificate
- https
- ingress-controller
---

Traekif can be used as an Ingress controller for Kubernetes, for k3s it is the default ingress controller.
<!--more-->

## Kubernetes resource annotations

When deployed as the Ingress controller in a Kubernetes cluster Traefik will watch for [specific annotations](https://doc.traefik.io/traefik/routing/providers/kubernetes-ingress/#annotations)
on Kubernetes `Ingress` and `Service` resources and use these to configure its routes.

## Example Ingress

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

### Ingress annotations

#### Entrypoints

Lists the name(s) of the specific Traefik entrypoints for the Ingress as per the Traefik configuration:
```yaml
traefik.ingress.kubernetes.io/router.entrypoints: ep1,ep2
```

#### Certificate Resolver

If `certResolver` is defined, Traefik will try to generate certificates based on routers Host & HostSNI rules from the 
configuration:
```yaml
traefik.ingress.kubernetes.io/router.tls.certresolver: myresolver
```

#### Middlewares

Used to define a middleware to use for the Ingress, the naming syntax of the middleware is:
`<NAMESPACE>-<MIDDLEWARE_NAME>@kubernetescrd`, for example:

```yaml
traefik.ingress.kubernetes.io/router.middlewares: longhorn-system-longhorn-auth@kubernetescrd
```

## Custom Resource Definitions (CRDs)

Traefik can use [Custom Resource Definitions (CRDs)](https://doc.traefik.io/traefik/routing/providers/kubernetes-crd/) to
configure the routing it performs within a Kubernetes cluster.

Traefik specific CDRs include:
* IngressRoute
* IngressRouteUDP
* MiddleWare

### IngressRoute

IngressRoute is the CRD implementation of a [Traefik HTTP router](https://doc.traefik.io/traefik/routing/routers/#configuring-http-routers).

```yaml
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
      match: Host(`argocd.example.com`)
      priority: 10
      services:
        - name: argocd-server
          port: 80
    - kind: Rule
      match: Host(`argocd.example.com`) && Headers(`Content-Type`, `application/grpc`)
      priority: 11
      services:
        - name: argocd-server
          port: 80
          scheme: h2c
  tls:
    # References an existing tls secret
    secretName: argocd-tls
```

### IngressRouteUDP

```yaml
apiVersion: traefik.containo.us/v1alpha1
kind: IngressRouteUDP
metadata:
  name: broadlink-discovery
  namespace: default
spec:
  entryPoints:
    # References a named entrypoint in the Traefik configuration
    - broadlink-udp
  routes:
    - services:
        - name: broadlink
          # UDP port
          port: 50110
          weight: 10
```

### Middleware

```yaml
apiVersion: traefik.containo.us/v1alpha1
kind: Middleware
metadata:
  name: basic-auth
  namespace: default
spec:
  basicAuth:
    # References an existing secret in the system
    secret: my-basic-auth
```

You can then use the middleware in an Ingress by applying the annotation:

```yaml
traefik.ingress.kubernetes.io/router.middlewares: default-basic-auth@kubernetescrd
```
