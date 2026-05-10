---
title: k3s etcdctl
tags:
 - k3s
 - etcd
---

`etcdctl` provides a CLI for interacting with etcd servers. Unfortunately K3s does not bundle `etcdctl` it needs to be 
installed manually.
<!--more-->

## Install

```shell
ETCD_VERSION="v3.5.5"
ETCD_URL="https://github.com/etcd-io/etcd/releases/download/${ETCD_VERSION}/etcd-${ETCD_VERSION}-linux-amd64.tar.gz"
curl -sL ${ETCD_URL} | sudo tar -zxv --strip-components=1 -C /usr/local/bin
```

## Using with k3s certificates

After installation, you can use `etcdctl` by configuring it to use the K3s-managed certificates and keys for authentication:
```shell
sudo etcdctl version \
  --cacert=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt \
  --cert=/var/lib/rancher/k3s/server/tls/etcd/client.crt \
  --key=/var/lib/rancher/k3s/server/tls/etcd/client.key
```

## Viewing etcd members

```shell
sudo etcdctl member list --write-out=table \
  --cacert=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt \
  --cert=/var/lib/rancher/k3s/server/tls/etcd/client.crt  \
  --key=/var/lib/rancher/k3s/server/tls/etcd/client.key 
```
```text
$ 
+------------------+---------+------------------------+---------------------------+-----------------------------+------------+
|        ID        | STATUS  |          NAME          |        PEER ADDRS         |       CLIENT ADDRS          | IS LEARNER |
+------------------+---------+------------------------+---------------------------+-----------------------------+------------+
| 3322056e99494079 | started | optiplex-7050-fb9bc235 | https://192.168.0.191:2380 | https://192.168.0.191:2379 |      false |
| 79497bfbadfc8853 | started | optiplex-3050-6815fac9 | https://192.168.0.142:2380 | https://192.168.0.142:2379 |      false |
| 8a597f69702de0c5 | started | optiplex-7040-0eb070e6 | https://192.168.0.133:2380 | https://192.168.0.133:2379 |      false |
+------------------+---------+------------------------+---------------------------+-----------------------------+------------+
```

## Remove etcd member

To remove a member from the etcd quorum use the members' ID:
```shell
sudo etcdctl member remove 3322056e99494079 \
  --cacert=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt \
  --cert=/var/lib/rancher/k3s/server/tls/etcd/client.crt  \
  --key=/var/lib/rancher/k3s/server/tls/etcd/client.key 
```
```text
Member 3322056e99494079 removed from cluster  44e4a7149e85789
```

## References

* [Using etcdctl - k3s](https://docs.k3s.io/advanced#using-etcdctl)