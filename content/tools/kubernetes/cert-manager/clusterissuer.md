---
title: "ClusterIssuer"
tags:
  - cert-manager
  - https
  - lets_encrypt
---

A [ClusterIssuer](https://cert-manager.io/docs/configuration/) in [cert-manager](../cert-manager) defines the 
Certificate Authority (CA) and the solver logic for the entire cluster.
<!--more-->

## ClusterIssuer for Let's Encrypt using DNS-01 challenge

A _ClusterIssuer_ is a cluster-scoped version of an _Issuer_, meaning it can be used to sign certificates for any namespace in the cluster.
This is generally the preferred approach for platform-wide services like Let's Encrypt.

### Cloudflare DNS provider example

The following configuration uses Cloudflare as the example DNS provider, though the structure remains similar for other providers.

```yaml
apiVersion: cert-manager.io/v1 # The API group and version for the cert-manager project
kind: ClusterIssuer # The resource type that defines a cluster-wide certificate authority
metadata:
  name: letsencrypt-dns-issuer # The unique name used to reference this issuer in Certificate resources
spec:
  acme: # Configuration block for the Automated Certificate Management Environment (ACME) protocol
    email: admin@example.com # The email address used by Let's Encrypt for registration and expiry notices
    
    server: https://acme-v02.api.letsencrypt.org/directory # The URL for the production Let's Encrypt ACME server
    
    privateKeySecretRef: # Reference to the secret where the ACME account private key will be stored
      name: letsencrypt-account-key # The name of the secret that cert-manager will create to store the account key
    
    solvers: # A list of challenge solvers used to prove domain ownership
    - selector: # An optional filter to determine which domains this specific solver should be used for
        dnsZones: # A list of DNS zones that this solver is authorized to manage
          - "example.com" # The specific domain zone that will be handled by this Cloudflare configuration
      
      dns01: # Configuration block for the DNS-01 challenge type
        cloudflare: # Specifies that the Cloudflare DNS provider will be used for the challenge
          email: admin@example.com # The email address associated with the Cloudflare account
          apiTokenSecretRef: # Reference to a secret containing the Cloudflare API token
            name: cloudflare-api-token-secret # The name of the Kubernetes Secret containing the API token
            key: api-token # The specific key within the Secret that holds the token value
```

### DuckDNS DNS webhook provider example

A webhook-based solver allows cert-manager to support DNS providers that are not included in its core codebase. 

Because DuckDNS uses a unique API for managing TXT records, you must deploy a separate webhook controller that acts as an adapter. 
When a certificate request is initiated, cert-manager sends a payload to this webhook, which then communicates with the 
DuckDNS API to create the required `_acme-challenge` record.

The following configuration defines a ClusterIssuer configured to use the DuckDNS webhook. 
This resource is cluster-scoped, meaning it can fulfill certificate requests across all namespaces while utilizing a single Let's Encrypt account.


```yaml
apiVersion: cert-manager.io/v1 # Specifies the API group and version for cert-manager resources
kind: ClusterIssuer # Defines this as a cluster-wide issuer rather than a namespace-scoped one
metadata:
  name: duckdns-cluster-issuer # The unique name used to identify this issuer within the cluster
spec:
  acme: # Begins the configuration for the Automated Certificate Management Environment protocol
    server: https://acme-v02.api.letsencrypt.org/directory # The production directory URL for Let's Encrypt
    email: your-email@example.com # The contact email for Let's Encrypt account registration and expiry alerts
    privateKeySecretRef: # Defines where to store the ACME account's private encryption key
      name: letsencrypt-duckdns-account-key # The name of the Secret created to hold the account private key
    solvers: # A list of mechanisms used to solve the ACME domain ownership challenges
    - dns01: # Indicates that the DNS-01 challenge type will be used for validation
        webhook: # Specifies that an external webhook will handle the DNS record manipulation
          groupName: acme.duckdns.org # The API group name that the DuckDNS webhook controller identifies with
          solverName: duckdns # The specific solver identifier recognized by the deployed webhook pod
          config: # A provider-specific configuration block passed directly to the webhook
            apiTokenSecretRef: # Reference to the Kubernetes Secret containing your DuckDNS credentials
              name: duckdns-token-secret # The name of the Secret where your DuckDNS API token is stored
              key: token # The specific key inside the Secret that contains the actual API token string
```

The `groupName` is a critical field because it tells cert-manager which external service is responsible for the challenge. 
When the cert-manager controller encounters this ClusterIssuer, it looks for a registered APIService or a matching webhook 
Pod that claims the `acme.duckdns.org` group. 
Once the link is established, the webhook receives the challenge token and the target domain. 
It then performs an HTTP request to the DuckDNS API using the token retrieved from your _apiTokenSecretRef_.

After the DuckDNS API confirms the TXT record is set, cert-manager waits for DNS propagation before notifying 
Let's Encrypt to perform the validation. 
Once validated, the TXT record is deleted via another webhook call, and the signed certificate is issued. 
This entire process is transparent to the user, who only needs to ensure that the webhook controller is running and that 
the Secret containing the DuckDNS token exists in the namespace where the webhook was installed.
