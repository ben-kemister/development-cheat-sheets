---
title: Secrets
tags:
  - kubernetes
  - secrets
---

This page contain information about Kubernetes [secrets](https://kubernetes.io/docs/concepts/configuration/secret/).
<!--more-->

## Types

There is many [different types of secrets](https://kubernetes.io/docs/concepts/configuration/secret/#secret-types), 
below is a non-exhaustive list of some common secret types:
* `Opaque` - the default Secret type if you don't explicitly specify a type.
* `kubernetes.io/basic-auth` - for storing credentials needed for basic authentication
* `kubernetes.io/tls` - data for a TLS client or server

## Creating secrets

The command below will create a generic (a.k.a. Opaque) secret:
```powershell
kubectl create secret generic my-generic-credentials `
    --from-literal=username=admin `
    --from-literal=password='password'
```

> Note the `--from-literal` flag specify a _key_ and literal _value_ to insert in secret (i.e. mykey=somevalue)
> It will automatically **base64 encode** the **value** input.

This will result in teh creation of the following secret:
```yaml
apiVersion: v1
kind: Secret
type: Opaque
metadata:
  name: my-generic-credentials
  namespace: default
data:
  password: cGFzc3dvcmQ=
  username: YWRtaW4=
```

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

## Secrets with dots in keys

If you have a secret where the value is held in a key that contains a dot `.` in the name, for example:
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

## Using a secret as a file

Create the file containing the secret(s):
```powershell
echo "database.password=change-me" > database.properties
```

Create the secret using the file as an input:
```powershell
kuebctl create secret generic database-creds `
    --from-file=database.properties
```

You can then reference the secret as a volume and volumeMount in the pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  volumes:
    - name: db-credentials
      secret:
        secretName: database-creds
  containers:
    - name: my-image
      ...
      volumeMounts: 
        - mountPath: /usr/local/app/credentials/database.properties
          name: database-creds
          subPath: database.properties
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

## TLS Secrets

The `kubernetes.io/tls` Secret type is for storing a certificate and its associated key that are typically used for TLS.

One common use for TLS Secrets is to configure encryption in transit for an **Ingress**, 
but you can also use it with other resources or directly in your workload. 

When using this type of Secret, the `tls.key` and the `tls.crt` key must be provided in the data (or stringData) field 
of the Secret configuration, although the API server doesn't actually validate the values for each key.

### Creating TLS Secret

You [create a TLS secret](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_create/kubectl_create_secret_tls/) 
from the given public/private key pair.

The public key certificate must be .PEM encoded and match the given private key.

The syntax is ``kubectl create secret tls NAME --cert=path/to/cert/file --key=path/to/key/file [--dry-run=server|client|none]``

Using this command will create a secret like:
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: secret-tls
type: kubernetes.io/tls
data:
  # values are base64 encoded, which obscures them but does NOT provide
  # any useful level of confidentiality
  tls.crt: |
    LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNVakNDQWJzQ0FnMytNQTBHQ1NxR1NJYjNE
    ...
    RklDQVRFLS0tLS0K    
  # In this example, the key data is not a real PEM-encoded private key
  tls.key: |
    RXhhbXBsZSBkYXRhIGZvciB0aGUgVExTIGNydCBmaWVsZA==    
```
