---
title: "SecretGenerator"
tags:
- kustomize
- secret
---

This page covers the use of kustomizes' SecretGenerator.
<!--more-->

## Generating Secrets from `.env` files

To generate Kubernetes secrets from an environment (`.env`) file using Kustomize, you define a secretGenerator in your 
`kustomization.yaml` file and reference the env file using the `envs` field.

Create a simple text file containing your key-value pairs, one per line.

```text
# database.env (This file should ideally be kept out of version control)
DB_USERNAME=admin
DB_PASSWORD=supersecretpassword123
API_KEY=sk-12345abcdef
```

Use the `secretGenerator` in your `kustomization.yaml` file:
```yaml
# kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

secretGenerator:
- name: database-creds
  envs:
    - database.env
      type: Opaque # Optional, Opaque is the default type
```

The output will be a Kubernetes Secret object with a name appended with a unique hash, ensuring a new secret is created 
when the content changes, which automatically triggers a rolling update of dependent pods.
```yaml
# Generated output (shortened for clarity)
apiVersion: v1
kind: Secret
metadata:
  name: database-creds-a1b2c3d4e5
type: Opaque
data:
    API_KEY: c2stMTIzNDVhYmNkZWY=
    DB_PASSWORD: c3VwZXJzZWNyZXRwYXNzd29yZDEyMw==
    DB_USERNAME: YWRtaW4=
```