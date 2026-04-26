---
title: BitWarden CLI
tags:
- kubernetes
- external_secrets_operator
---

This page contains information about using secrets from the Bitwarden Password Manager with the External Secrets Operator (ESO).
<!--more-->
This is done using the BitWarden CLI as described on [Bitwarden support using webhook provider](https://external-secrets.io/latest/examples/bitwarden/).

## Authentication via API Key

It is possible to use an API keys (and client secret) to authenticate with Bitwarden as described [here](https://bitwarden.com/help/personal-api-key/).
This is very handy when used as an External Secrets Operator provider as it does not require any 2FA to retrieve the 
secrets via the CLI.

You will need a Secret with the following details:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: bitwarden-cli
  namespace: bitwarden
  annotations:
    kubernetes.io/description: "Used by the BitWarden CLI to retrieve secret values from the BitWarded Password Manager"
type: Opaque
stringData:
  # BitWarden Password Manager URL: https://vault.bitwarden.com/#/vault
  BW_HOST: "https://vault.bitwarden.com"
  BW_USERNAME: "<EMAIL_ADDRESS>"
  BW_PASSWORD: "<PASSWORD>"
  BW_CLIENTID: "<CLIENT_ID>"
  BW_CLIENTSECRET: "<CLIENT_SECRET"
```

This can be feed into your Deployment as follows:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bitwarden-cli
  namespace: bitwarden
  labels:
    app.kubernetes.io/instance: bitwarden-cli
    app.kubernetes.io/name: bitwarden-cli
  annotations:
    kubernetes.io/description: "Run the BitWarden CLI as a part of the External Secrets Operator (ESO)"
spec:
  ...
  template:
    ...
    spec:
      containers:
        - name: bitwarden-cli
          image: ghcr.io/charlesthomas/bitwarden-cli:2026.3.0
          imagePullPolicy: IfNotPresent

          envFrom: # Added environment variable injection from Secret
            - secretRef:
                name: bitwarden-cli
```

## Handy Links

* [Bitwarden Password Manager CLI](https://bitwarden.com/help/cli/)
* [Bitwarden clients - GitHub](https://github.com/bitwarden/clients)
