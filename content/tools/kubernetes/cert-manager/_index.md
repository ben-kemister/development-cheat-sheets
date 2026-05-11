---
title: "Cert-Manager"
date: 2024-01-06T06:56:47+11:00
tags:
- kubernetes
- certificate
- cert-manager
- https
- lets_encrypt
---

[cert-manager](https://cert-manager.io/) is a cloud native certificate manager.
<!--more-->
Cert-manager functions as a native Kubernetes controller that automates the management and issuance of TLS certificates 
from various issuing sources. 
It ensures that certificates are valid and up to date, and it attempts to renew certificates at a configured time before expiry. 
By watching for specific Custom Resource Definitions (CRDs) within the cluster, it orchestrates the entire lifecycle of 
a certificate, from the initial request to the final injection into a Kubernetes Secret.

As of early 2024 it is a CNCF Incubating Project in the [Security & Compliance](https://landscape.cncf.io/card-mode?category=security-compliance&grouping=category&zoom=120) 
category.

## Topic specific sub-pages

{{% children sort="title" description="true" %}}

## Core Mechanism of cert-manager

The operations of cert-manager revolve around a set of controllers that monitor the state of the Kubernetes cluster. 
When you define a _Certificate_ resource, cert-manager recognizes the intent to secure a service. 
It doesn't generate the certificate itself but instead acts as a broker between Kubernetes and a Certificate Authority (CA) like Let’s Encrypt. 
The process begins when the controller sees a _Certificate_ resource and generates a _CertificateRequest_. 
This request is a base64 encoded CSR (Certificate Signing Request) that represents the public part of the key pair.

For ACME-based issuers, cert-manager further breaks this down into _Order_ and _Challenge_ resources. 
The _Order_ resource tracks the progress of the request with the ACME server, while the _Challenge_ resource manages the 
specific task of proving domain ownership. 

After proving ownership of the domain, cert-manager takes the issued certificate and stores it in a standard 
Kubernetes TLS Secret, making it available for use by Ingress controllers or other workloads.

### DNS-01 Solver Logic

The DNS-01 challenge is particularly powerful because it allows you to issue certificates for internal services that 
aren't reachable from the public internet, as well as for wildcard domains.

In a DNS-01 scenario, the challenge controller interacts with your DNS provider’s API to insert a temporary TXT record. 
Once the ACME server verifies this record, the challenge is marked as successful, the TXT record is cleaned up, 
and the ACME server issues the signed certificate. 

Once the challenge is triggered, cert-manager fetches the necessary token from Let's Encrypt (or another provider) and 
uses the credentials provided in a Kubernetes Secret to authenticate with your DNS provider 
(such as Cloudflare, AWS Route53, or Google Cloud DNS) and create the record at `_acme-challenge.yourdomain.com`.

## Key Resource Definitions

The following table outlines the primary resources managed by cert-manager during the issuance process.

| Resource           | Scope     | Purpose                                                                                |
|--------------------|-----------|----------------------------------------------------------------------------------------|
| ClusterIssuer      | Cluster   | Defines the CA (like Let's Encrypt) and the solver logic for the entire cluster.       |
| Certificate        | Namespace | A human-readable definition of a desired certificate, including domains and secrets.   |
| CertificateRequest | Namespace | The internal resource that holds the CSR and tracks the signing process.               |
| Order              | Namespace | An ACME-specific resource that manages the state of a certificate request with the CA. |
| Challenge          | Namespace | The most granular resource, responsible for a single DNS or HTTP validation task.      |


