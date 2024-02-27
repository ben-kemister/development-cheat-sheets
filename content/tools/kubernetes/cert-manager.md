---
title: "Cert-Manager"
date: 2024-01-06T06:56:47+11:00
tags:
- kubernetes
- traefik
- certificate
- cert-manager
- https
- lets_encrypt
---

[cert-manager](https://cert-manager.io/) is a cloud native certificate manager.
<!--more-->
cert-manager adds certificates and certificate issuers as resource types in Kubernetes clusters, 
and simplifies the process of obtaining, renewing and using those certificates.

As of early 2024 it is a CNCF Incubating Project in the [Security & Compliance](https://landscape.cncf.io/card-mode?category=security-compliance&grouping=category&zoom=120) 
category.

## Example Ingress 

Below is a simple example of an Ingress which uses the `cert-manager-cert-manager-webhook-duckdns-staging` Cluster certificate
issuer to create a secret named `whoami-tls` which will contain the `tls.crt` and `tls.key` for the host 
`whoami.<YOUR_SUB_DOMAIN>.duckdns.org`

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