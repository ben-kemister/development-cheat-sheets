---
title: ExternalSecrets
author: AI Assistant
tags:
- kubernetes
- external-secrets
- secrets
- devops
- infrastructure
- rbac
---

This document provides a technical overview of the [ExternalSecret](https://external-secrets.io/latest/api/externalsecret/) 
Kubernetes Custom Resource Definition (CRD) managed by the External Secrets Operator (ESO),
<!--more-->
detailing its function, operational policies, and key configuration aspects for synchronizing secrets from external 
providers into Kubernetes Secrets.

## Core Concepts and Functionality

The ExternalSecret CRD serves as a declarative bridge, instructing the External Secrets Operator on which external data 
should be fetched and subsequently stored as a standard Kubernetes `Secret`. 
It specifies how the external secret data should be transformed and formatted before being applied to the cluster.

The primary mechanisms for fetching external data are `spec.data`, which explicitly lists individual keys to sync, 
and `spec.dataFrom`, which instructs the operator to retrieve all values from the external API. 
The operator then uses a `spec.target.template` as a blueprint to construct the new `Secret`, allowing for 
transformation of values using [Go templating](../../../languages/go/templating) syntax or by referencing ConfigMaps via `templateFrom`.

## Update Behavior and Refresh Policies

The timing and conditions under which the ExternalSecret resource triggers updates to the corresponding Kubernetes Secret 
are governed by the `spec.refreshPolicy` field. This field defines the synchronization behavior, and if not explicitly set, 
the default behavior is `Periodic`.

- `CreatedOnce`: When this policy is selected, the controller creates the Secret only if it is not already present. 
    It will not perform any updates to the secret even if the source data changes. 
    The secret will only be updated or recreated if the original ExternalSecret resource itself is changed or deleted. 
    This policy is ideal for managing immutable credentials or when manual control over updates is preferred.
- `Periodic`: This is the default mode. The controller creates the secret if it is missing, and subsequently updates it at a regular interval defined by the `spec.refreshInterval`. 
    If the interval is set to zero, the secret is created once and will never be updated.
- `OnChange`: With this policy, the controller only creates the Secret if it doesn't exist. 
    It will then only update the secret if the metadata or the specification of the ExternalSecret resource is modified. 
    This policy is useful when a user wants to maintain manual control over when the secret is refreshed, independent of a set time interval.

## Ownership and Deletion Policy

Regarding the lifecycle of the Kubernetes Secret, the External Secrets Operator can be configured to manage ownership of 
the secret it generates. 
To ensure a Kubernetes Secret is automatically deleted when its corresponding ExternalSecret is removed, 
the `creationPolicy` must be set to `Owner`. When this policy is used, the operator sets `ownerReferences` on the generated Secret. 
Consequently, Kubernetes' garbage collector automatically deletes the Secret when the ExternalSecret resource is deleted.

Conversely, the `deletionPolicy` attribute pertains to what the operator does when a remote backend secret 
(e.g., in AWS Secrets Manager) is deleted, not what happens to the local Kubernetes Secret when the ExternalSecret resource is removed.

## Generators and Providers

The operator supports numerous external secret stores and generation methods.

Providers include support for cloud services such as AWS Secrets Manager, Azure Key Vault, and Google Cloud Secret Manager, 
as well as dedicated tools like HashiCorp Vault, CyberArk Conjur, and various third-party integrations like 
1Password Connect Server and Bitwarden Secrets Manager.

Generators offer various ways to create secrets, such as using AWS STS Session Tokens, passwords, SSH keys, GitHub credentials, 
or even generating "fake" secrets for development and testing.


## Examples

### Basic ExternalSecret Mapping

This example shows a simple mapping where a single value from a remote provider is fetched and placed into a specific 
key within the resulting Kubernetes Secret using a template.

```yaml
apiVersion: external-secrets.io/v1
kind: ExternalSecret
metadata:
  name: example-basic-secret
spec:
  # The secret is created once and will not be updated even if the source changes
  refreshPolicy: CreatedOnce

  target:
    name: my-app-secret
      
    creationPolicy: Owner
        
    template:
      type: Opaque
      data:
        # Mapping the fetched 'client_secret' to the 'CLIENT_SECRET' key in K8s
        CLIENT_SECRET: "{{ .client_secret }}"
    
  data:
    - secretKey: client_secret # This name is referenced in the template above
      sourceRef:
        storeRef:
          name: my-vault-store
          kind: ClusterSecretStore
      remoteRef:
        key: "REDACTED_ID" # The unique identifier in your external provider
        property: password
```
### Advanced Templating with `templateFrom`

This example demonstrates how to use `templateFrom` and Go templating logic to transform data (e.g. performing a hash) 
before it is stored in Kubernetes.

```yaml
apiVersion: external-secrets.io/v1
kind: ExternalSecret
metadata:
  name: example-template-secret
spec:
  refreshPolicy: CreatedOnce
    
  target:
    name: my-complex-secret
    creationPolicy: Owner
        
    template:
      type: Opaque
      templateFrom:
        - target: Data
          # Using Go templating to generate a hashed password from the raw values
          literal: |-
            USER_NAME: {{ .user }}
            USER_PASS_HASH: {{ index (slice (htpasswd .user .pass "bcrypt" | splitList ":" ) 1) 0 }}
    
  data:
    - secretKey: user
      sourceRef:
        storeRef:
          name: my-vault-store
          kind: ClusterSecretStore
      remoteRef:
        key: "REDACTED_ID"
        property: username
    - secretKey: pass
      sourceRef:
        storeRef:
          name: my-vault-store
          kind: ClusterSecretStore
      remoteRef:
        key: "REDACTED_ID"
        property: password
```

## Further Information and Resources

For more in-depth knowledge on managing these secrets and the operator's functionality, the following resources are available.

* [External Secrets Operator API Reference](https://external-secrets.io/latest/api/externalsecret/)
- [External Secrets Operator Ownership & Deletion Guide](https://external-secrets.io/guides-ownership-deletion-policies/ )
- [Kubernetes Secrets Overview](https://kubernetes.io/concepts/configuration/secret) 