---
title: (Kubernetes) Operator
tags:
- database
- mysql
- operator
- kubernetes
---

The [MySQL Operator for Kubernetes](https://dev.mysql.com/doc/mysql-operator/en/) manages MySQL InnoDB Cluster setups inside a Kubernetes Cluster. 
<!--more-->
MySQL Operator for Kubernetes manages the full lifecycle with setup and maintenance including automating upgrades and backups.

## Installing the Operator

I found the easiest way to do this so that I could apply my own customisations for my setup was to use a helm chart within kustomise:

```yaml
#
# Description: This is a Kustomise file for deploying the mysql operator to a cluster.
#
# References:
# - https://dev.mysql.com/doc/mysql-operator/en/mysql-operator-installation-helm.html
#
# To view the results use:
#   kubectl kustomize --enable-helm mysql-operator > mysql-operator/test.yaml
#
# To apply run:
#   kubectl kustomize --enable-helm mysql-operator | kubectl create -f -
#
---
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

namespace: mysql-operator

helmCharts:
  - name: mysql-operator
    repo: https://mysql.github.io/mysql-operator/
    releaseName: mysql-operator
    namespace: mysql-operator
    version: 2.2.1
    valuesFile: ./mysql-operator-values.yaml
    includeCRDs: true

resources:
  - manifests/00-mysql-operator-ns.yaml

patches:
  #  Make sure to sync the CRDs before anything else (to avoid sync failures)
  #  Use replace strategy for CRDs, as often CRDs are too big fit into the last-applied-configuration annotation that kubectl apply wants to use
  - target:
      kind: CustomResourceDefinition
    patch: |-
      - op: add
        path: /metadata/annotations/argocd.argoproj.io~1sync-wave
        value: -5
      - op: add
        path: /metadata/annotations/argocd.argoproj.io~1sync-options
        value: Replace=true
```

## Installing a Cluster

```yaml
#
# kubectl -n guacamole apply -f guacamole/manifests/mysql-cluster.yaml
#
# Refs:
# - https://dev.mysql.com/doc/mysql-operator/en/mysql-operator-innodbcluster-common.html
# - https://dev.mysql.com/doc/mysql-operator/en/mysql-operator-properties.html#mysql-operator-spec-innodbcluster
#
---
apiVersion: mysql.oracle.com/v2
kind: InnoDBCluster
metadata:
  name: guacamole-mysql-cluster
spec:
  # The secret containing the DB credentials
  secretName: guacamole-mysql
  tlsUseSelfSigned: true
  instances: 1
  router:
    instances: 1
  datadirVolumeClaimTemplate:
    accessModes:
      - ReadWriteOnce
    resources:
      requests:
        storage: 5Gi
```

## Database Init scripts

There doesn't seem to be an easy way to run initialisation scripts against a newly created cluster/database.

One way to do this is to stand up a temporary pod and run the commands/scripts from there. For example:

```yaml
#
# Description: A ConfigMap containing the script to run
#
--- 
apiVersion: v1
kind: ConfigMap
metadata:
  name: guacamole-mysql-initialization
data:
  first-db.sql: |-
    CREATE DATABASE IF NOT EXISTS guacamole_db DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
    use guacamole_db;
---
```

```yaml
#
# Description:
#
# Run from within the pod:
#    mysql -h $MYSQL_HOSTNAME -u root -p$MYSQL_ROOT_PASSWORD < init/first-db.sql
#
---
apiVersion: v1
kind: Pod
metadata:
  name: mysql-init-client
  namespace: guacamole
spec:
  containers:
    - name: mysql
      image: mysql:8.0
      ports:
        - containerPort: 80
      env:
        - name: MYSQL_ALLOW_EMPTY_PASSWORD
          value: "true"
        - name: MYSQL_HOSTNAME
          # Matches the name of the mysql-cluster
          value: guacamole-mysql-cluster
        - name: MYSQL_PORT
          value: "3306"
        - name: MYSQL_DATABASE
          value: "guacamole_db"
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: guacamole-mysql
              key: rootPassword
      volumeMounts:
        - name: config-volume
          mountPath: /init/
  volumes:
    - name: config-volume
      configMap:
        # Provide the name of the ConfigMap containing the files you want to add to the container
        name: guacamole-mysql-initialization
```