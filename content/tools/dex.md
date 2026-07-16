---
title: "Dex"
date: :
tags: ["OIDC", "OAuth2", "Identity", "Kubernetes", "Security"]
---

[Dex](https://dexidp.io/) is a lightweight, federated OpenID Connect (OIDC) identity service that acts as a single authentication 
layer by translating various upstream identity sources into a standardized OIDC interface.
<!--more-->

## Overview

Dex serves as a gateway for application authentication. Instead of requiring every application to support multiple 
protocols like SAML or LDAP, applications only need to implement OpenID Connect. 
Dex sits in front of your existing identity providers (IdPs) and handles the complexity of different authentication methods, 
presenting them to your services as a consistent, modern OIDC endpoint.

Because Dex does not maintain its own user database—instead delegating authentication to upstream providers—it is considered 
a "protocol adapter" rather than a full-fledged Identity and Access Management (IAM) platform. 
This makes it exceptionally lightweight and easy to deploy in cloud-native environments.

## Core Features

* **Single Protocol Interface:** All downstream applications speak only OIDC, regardless of how complex the upstream authentication is.
* **Lightweight & Portable:** Delivered as a single Go binary with minimal dependencies. It does not require a heavy JVM or a mandatory dedicated database.
* **Kubernetes Native:** Designed for modern infrastructure with official Helm charts and a small footprint, making it ideal for running alongside workloads in Kubernetes.
* **Stateless Identity:** Dex does not own user state; it transforms identities from external sources into standard OIDC claims on the fly.

## Supported Connectors

Dex supports a wide range of "pluggable" connectors, allowing you to bridge legacy and modern identity systems. Supported upstream providers include:

* **Standard Protocols:** LDAP, SAML 2.0, OpenID Connect, OAuth 2.0.
* **Cloud Providers:** Google, GitHub, GitLab, Microsoft, Atlassian.
* **Other Sources:** Gitea, AuthProxy, and more.

## Architecture and Storage

Dex functions as a portal between the user and the identity source. The flow typically follows:
`User` --> `OIDC Client (App)` --> `Dex` --> `Upstream Connector (e.g., LDAP)` --> `Identity Provider`.

While Dex is lightweight, it can use different backends to store its own state (such as client information or session data):

* **In-memory:** Best for local development or testing.
* **SQLite:** A simple file-based SQL database.
* **SQL Databases:** Support for PostgreSQL and MySQL for production environments.
* **Distributed/Cloud-Native:** Support for `etcd` or Kubernetes-native resources.

## Configuration Example

Configuration is handled via a YAML file where you define your issuer, storage, web settings, and connectors. 
Below is an example of a development configuration using a local SQLite database.

```yaml
# The canonical URL that all clients MUST use to refer to dex.
issuer: http://127.0.0.1:5556/dex

# Storage configuration for Dex state
storage:
  type: sqlite3
  config:
    file: examples/dex.db

# HTTP configuration for the Dex service
web:
  http: 0.0.0.0:5556

# OAuth2 settings
oauth2:
  # The allowed authorization flows
  grantTypes:
  - "authorization_code"
  - "refresh_token"
  
  # Allowed response types
  responseTypes: [ "code" ]

  # Default SSO sharing policy for clients
  ssoSharedWithDefault: "none"

# Example connector configuration (Mock/Local for development)
connector:
  type: static
  config:
    id: local-user
    name: "Local User"
    username: "admin"
    password: "password"
    userID: "12345"
```

## Handy Links

* [Dex Official Website](https://dexidp.io/)
* [Dex Documentation](https://dexidp.io/docs/)
* [Dex GitHub Repository](https://github.com/dexidp/dex)

