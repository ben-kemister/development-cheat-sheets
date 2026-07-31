---
title: "OAuth2 Proxy"
date: "2026-08-01"
tags: [OAuth2, OIDC, Reverse Proxy, Authentication, CNCF]
---

[OAuth2 Proxy](https://oauth2-proxy.github.io/oauth2-proxy/) is a flexible reverse proxy and static file server that 
provides authentication via various identity providers to secure web applications. 
<!--more-->

## Overview

OAuth2 Proxy acts as either a standalone reverse proxy or as middleware integrated into existing load balancers. 
It intercepts incoming requests and redirects users to an OAuth2 or OpenID Connect (OIDC) provider for authentication. 
Once authenticated, it forwards user details—such as email, domain, or group membership—to upstream applications via HTTP headers.

## Core Features

* **Multi-Provider Support**: Supports generic OIDC clients as well as specialized implementations for Google, Microsoft Entra ID, GitHub, and login.gov.
* **Identity Propagation**: Extracts user details (e.g., preferred usernames, groups) and passes them as HTTP headers to upstream services.
* **Flexible Deployment**: Can function as a standalone service or integrated middleware.
* **Cloud Native**: It is a [Cloud Native Computing Foundation (CNCF)](https://www.cncf.io/) Sandbox project.

## Architecture

The proxy sits between the client and the application, managing the authentication flow and session state. 
By handling the complexity of OAuth2/OIDC handshakes, it allows backend applications to remain agnostic of the specific identity provider being used.

## Deployment via Helm

* Repository: https://oauth2-proxy.github.io/manifests
* Chart Name: oauth2-proxy
* Image Registry: quay.io/oauth2-proxy/oauth2-proxy

### Installation & Management

To add the repository and install the chart:

```shell
helm repo add oauth2-proxy https://oauth2-proxy.github.io/manifests
helm install <release-name> oauth2-proxy/oauth2-proxy
```

To remove the deployment:

```shell
helm uninstall <release-name>
```

## Configuration Example

A typical deployment using Kustomize and Helm involves three main components: 
1. the proxy configuration file, 
2. the Helm values file, and
3. the Kustomize orchestration file.

### 1. Identity Provider Configuration (`oauth2_proxy.cfg`)

This file defines how `oauth2-proxy` interacts with your Identity Provider (IdP) and how user identity is propagated to 
upstream applications via HTTP headers.

```cfg
# OIDC Provider Settings
provider="oidc"
# The issuer URL must match the issuer URL returned by your OIDC provider
oidc_issuer_url="https://your-oidc-provider.example.com"
# This must match the redirect URI registered in your OIDC provider (e.g., in Dex or Google)
redirect_url="https://oauth2-proxy.example.com/oauth2/callback" 
upstreams = ["static://200"]
# Cookie Settings
cookie_secure=true # Set to true when using HTTPS (recommended for production) 
cookie_domains = [".example.com"] 
cookie_samesite="lax"
# Header Propagation
# Enables passing user information (email, etc.) to upstream services via HTTP headers
reverse_proxy=true 
pass_authorization_header=true 
pass_access_token=true 
pass_user_headers=true 
set_authorization_header=true 
set_xauthrequest=true
 pass_host_header=true
# Security
# Skip the sign-in page to automatically redirect to the login flow
skip_provider_button=true 
whitelist_domains = [".example.com"]
```

For more information on configuration options see: https://oauth2-proxy.github.io/oauth2-proxy/configuration/overview/

### 2. Helm Values (`values.yaml`)

The `values.yaml` file is used to override Helm chart defaults. It allows you to inject existing Kubernetes Secrets 
(for credentials) and ConfigMaps (for configuration files).

```yaml
# Configuration overrides
config:
  # Use an existing Secret containing OAuth2 credentials (client_id, client_secret)
  existingSecret: oauth2-proxy-secrets
  # Use an existing ConfigMap containing the configuration file content
  existingConfig: oauth2-proxy-config
  
  ingress: 
    enabled: true 
    className: traefik 
    annotations: 
      # Example: Use cert-manager to automate TLS certificate management 
      cert-manager.io/cluster-issuer: letsencrypt-prod 
    hosts: 
      - oauth2-proxy.example.com 
    tls: 
      - secretName: oauth2-proxy-tls 
        hosts: 
          - oauth2-proxy.example.com
```

### 3. Kustomize Orchestration (`kustomization.yaml`)

Kustomize can be used to package the Helm chart and provide the necessary configuration files using a `ConfigMapGenerator`.

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1 
kind: Kustomization

namespace: oauth2-proxy

# Deploy the official Helm chart using our custom values file
helmCharts:
  - name: oauth2-proxy 
    repo: 'https://oauth2-proxy.github.io/manifests' 
    releaseName: oauth2-proxy 
    namespace: oauth2-proxy 
    version: 10.7.0 
    valuesFile: ./values.yaml

# Generate a ConfigMap from the configuration file defined above
configMapGenerator:
  - name: oauth2-proxy-config 
    options: 
      disableNameSuffixHash: true 
    files:
    - config/oauth2_proxy.cfg
```

## Handy Links

* [Official Documentation](https://oauth2-proxy.github.io/oauth2-proxy/)
* [GitHub Repository](https://github.com/oauth2-proxy/oauth2-proxy)
* [Helm Chart](https://oauth2-proxy.github.io/manifests)
* [OAuth2-Proxy configuration](https://oauth2-proxy.github.io/oauth2-proxy/configuration/overview/)
* [Securing K3s Applications with OAuth2-Proxy: A Complete Implementation Guide](https://medium.com/@ayoubseddiki132/securing-k3s-applications-with-oauth2-proxy-a-complete-implementation-guide-0bab37c72a0e)

