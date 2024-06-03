---
title: Secrets
tags:
  - kubernetes
  - secrets
---

This page contain information about Kubernetes [secrets](https://kubernetes.io/docs/concepts/configuration/secret/).
<!--more-->

## Types

Below is a non-exhaustive list of secret types:
* `Opaque` - the default Secret type if you don't explicitly specify a type.
* `kubernetes.io/basic-auth` - for storing credentials needed for basic authentication

## Creating Opaque Secrets

```powershell
kubectl create secret generic my-generic-credentials `
    --from-literal=username=admin `
    --from-literal=password='password'
```

> Note the `--from-literal` flag specify a _key_ and literal _value_ to insert in secret (i.e. mykey=somevalue)
> It will automatically **base64 encode** the **value** input.

## Viewing and Decoding a secret

> The data values in a secret are **base64 encoded**!

```shell
kubectl get secret my-generic-credentials -o jsonpath='{.data}'
```
This will show the base64 encoded data fields of the secret:
```text
{"password":"cGFzc3dvcmQ=","username":"YWRtaW4="}
```

You can decode these values to see the original value:

In PowerShell:
```powershell
kubectl get secret my-generic-credentials -o jsonpath='{.data.password}' | %{ [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($_)) }
```
```text
password
```

Or in Linux (sh/bash) with:
```shell
kubectl get secret my-generic-credentials -o jsonpath='{.data.password}' | base64 --decode
```

### Secrets with dots in keys

If you have a secret where the value is held in a key that contains a dor `.` in the name, for example:
```yaml
kind: Secret
data:
  my.password: BASE64_ENCODED_PASSWORD_STRING
```

To decode ``my.password`` you can escape the dots with a backslash such as:
```shell
kubectl get secret my-generic-credentials -o "jsonpath='{.data['my\.password']}" | base64 --decode
```

## Using a secret in an Environmental variable

Create the secret, for example:
```powershell
kubectl create secret generic grafana-admin-credentials `
--from-literal=password=<YOUR_PASSWORD>
```

Then you can reference the secret in the pod like so:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: grafana-867f566db4-7ppjt
spec:
  containers:
  - env:
    # The environmental name to be exposed in the Pod
    - name: GF_SECURITY_ADMIN_PASSWORD
      valueFrom:
        secretKeyRef:
          # The name of the Secret within the same namespace as the pod
          name: grafana-admin-credentials
          # The key within the secret which contains the secret value
          key: password
...
```

## Creating Secrets for use with http basic-auth

There are two was you can create secrets for use as http basic-auth in Ingress objects:
1. Create a [basic-auth secret]() - which just base64 encodes the credentials, or
2. Create a _generic_/`Opaque` secret using `htpasswd` hashed values (see below for details)

### Generic/Opaque secret

You can create a generic/Opaque secret in a secure way (which hashes the values) is to use [htpasswd](https://httpd.apache.org/docs/2.4/programs/htpasswd.html).

#### Create the htpasswd file

> The syntax for htpasswd is `htpasswd [-c] passwdfile username`.  
> If you run the htpasswd command without the `-c` flag, it will **add** an entry to the file rather than creating a 
> new file from scratch.  
> This is useful if you want to add more credentials to the file.

```shell
htpasswd -c users admin
```
```text
New password:
Re-type new password:
Adding password for user admin
```

#### Create generic/Opaque secret

```shell
kubectl create secret generic my-basic-auth \
--from-file=users
```

#### Updating the Secret

If you need to update the secret in the future you can use this trick:

```shell
kubectl create secret generic my-basic-auth \
--from-file=users --dry-run -o yaml | \
kubectl apply -f -
```
