---
title: "Ingress Annotation"
tags:
- cert-manager
- ingress
- annotation
---

This explains how the ingress-shim controller in [cert-manager](../cert-manager) detects annotations on an Ingress to
automatically generates a Certificate resource and the automated creation and renewal of the required TLS Secret.
<!--more-->

## How it works

cert-manager has a specialized internal component called the **ingress-shim** which watches the Kubernetes API for _Ingress_
resources that contain cert-manager-specific annotations.

When it detects the `cert-manager.io/cluster-issuer` or `cert-manager.io/issuer` annotation, it assumes responsibility
for the lifecycle of the TLS certificates required by that Ingress.

The ingress-shim parses the `tls` block within your Ingress specification.
It extracts the hostnames listed in the `hosts` field and the target secret name from the `secretName` field.

Using this metadata, the shim automatically generates a _Certificate_ resource in the same namespace as the _Ingress_.
This generated _Certificate_ acts as the "source of truth" that triggers the standard cert-manager workflow,
including the creation of _CertificateRequests_ and the fulfillment of ACME challenges.

Once the Certificate Authority (such as Let's Encrypt) validates the challenge and issues the certificate, c
ert-manager populates the Kubernetes _Secret_ defined in the Ingress.
The Ingress controller (such as NGINX or Traefik) is already watching that Secret; as soon as the data appears,
the controller live-reloads its configuration to serve the application over HTTPS.

### Example Ingress

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