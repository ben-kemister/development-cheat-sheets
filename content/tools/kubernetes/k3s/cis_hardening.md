---
title: "CIS Hardening"
tags:
  - k3s
  - cis
  - hardening
---

This page contains information about hardening (and verifying a hardened) k3s cluster.
<!--more-->

## Verifying Encryption of Secrets

### Verify Secrets encryption is enabled

Ensure that you have started the server(s) with [secrets encryption enabled](./server).

> WARNING: Starting K3s without encryption and enabling it at a later time is currently not supported.
> See [k3s secrets-encrypt documentation](https://docs.k3s.io/cli/secrets-encrypt).

You can check if secrets encryption is enabled with:
```shell
$ sudo k3s secrets-encrypt status
Encryption Status: Enabled
Current Rotation Stage: start
Server Encryption Hashes: All hashes match

Active  Key Type  Name
------  --------  ----
 *      AES-CBC   aescbckey
```

### Verify that new Secrets are encrypted (at rest)

1. Install an `etcd` client:
    ```shell
    # Install etcdctl (A simple command line client for etcd)
    sudo apt install etcd-client
    ```

2. Create a new secret to check:
    ```shell
    $ sudo k3s kubectl create secret generic secret1 -n default --from-literal=mykey=mydata
    secret/secret1 created
    ```

3. Using the `etcd` client read that Secret out of `etcd`:
    ```shell
    sudo ETCDCTL_API=3 etcdctl \
       --cacert=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt  \
       --cert=/var/lib/rancher/k3s/server/tls/etcd/client.crt \
       --key=/var/lib/rancher/k3s/server/tls/etcd/client.key  \
       get /registry/secrets/default/secret1 | hexdump -C
    ``` 
   Output will be similar to:
    ```text
    00000000  2f 72 65 67 69 73 74 72  79 2f 73 65 63 72 65 74  |/registry/secret|
    00000010  73 2f 64 65 66 61 75 6c  74 2f 73 65 63 72 65 74  |s/default/secret|
    00000020  31 0a 6b 38 73 3a 65 6e  63 3a 61 65 73 63 62 63  |1.k8s:enc:aescbc|
    00000030  3a 76 31 3a 61 65 73 63  62 63 6b 65 79 3a b6 a1  |:v1:aescbckey:..|
    ...
    ```

4. Verify that the stored secret (secret1) is prefixed with `k8s:enc:aescbc:v1:`, 
   which indicates the `aescbc` provider has encrypted the resulting data.
5. (Optional) You can confirm that the encryption key (`aescbckey` in this example) matches the one in the 
   encryption config file: `/var/lib/rancher/k3s/server/cred/encryption-config.json`

## Pod Security Admission (PSA)

As per the [k3s documentation](https://docs.k3s.io/security/hardening-guide#pod-security); you can use 
[Pod Security Admissions (PSAs)](../resources/admission_controller) for controlling pod security.

The policy should be written to a file in `/var/lib/rancher/k3s/server/psa.yaml`.

Add enabled by passing the following flag to the K3s server: `--kube-apiserver-arg="admission-control-config-file=/var/lib/rancher/k3s/server/psa.yaml"`

For more information see:
* [Admission Controller page](../resources/admission_controller)
* [k3s CIS Hardening Guide - k3s](https://docs.k3s.io/security/hardening-guide#pod-security)
* [Pod Security Levels - Kubernetes](https://kubernetes.io/docs/concepts/security/pod-security-admission/#pod-security-levels)
* [Pod Security Standards - Kubernetes](https://kubernetes.io/docs/concepts/security/pod-security-standards/)

### Testing PodSecurityConfiguration

### Example of Warning

```shell
$ kubectl run tmp-shell --rm -i --tty --image redhat/ubi8
Warning: would violate PodSecurity "restricted:latest": allowPrivilegeEscalation != false (container "tmp-shell" must set securityContext.allowPrivilegeEscalation=false), unrestricted capabilities (container "tmp-shell" must set securityContext.capabilities.drop=["ALL"]), runAsNonRoot != true (pod or container "tmp-shell" must set securityContext.runAsNonRoot=true), seccompProfile (pod or container "tmp-shell" must set securityContext.seccompProfile.type to "RuntimeDefault" or "Localhost")
```

### Example of failure

Example of a DaemonSet which attempted to create a Pod which violates the PodSecurityConfiguration:

```text
Warning  FailedCreate  4m33s                 daemonset-controller  Error creating: pods "metallb-speaker-jvvwx" is forbidden: violates PodSecurity "baseline:latest": 
non-default capabilities (containers "speaker", "frr" must not include "NET_ADMIN", "NET_RAW", "SYS_ADMIN" in securityContext.capabilities.add), 
host namespaces (hostNetwork=true), hostPort (containers "speaker", "frr-metrics" use hostPorts 7472, 7473, 7946)
```


## Links & References

* [CIS Hardening Guide - k3s](https://docs.k3s.io/security/hardening-guide)
* [Secrets Encryption Config - k3s](https://docs.k3s.io/security/secrets-encryption)
* [k3s secrets-encrypt - k3s](https://docs.k3s.io/cli/secrets-encrypt)
* [Verify that newly written data is encrypted - Kubernetes](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/#verifying-that-data-is-encrypted)