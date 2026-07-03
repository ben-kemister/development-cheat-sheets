---
title: Harbor
tags:
- container
- image_registry
---

[Harbor](https://goharbor.io/) is an open source registry that secures artifacts with policies and role-based access control, 
ensures images are scanned and free from vulnerabilities, and signs images as trusted. 
<!--more-->
Harbor, a CNCF Graduated project, delivers compliance, performance, and interoperability to help you consistently and 
securely manage artifacts across cloud native compute platforms like Kubernetes and Docker.

## Configure Proxy Cache

Proxy cache allows you to use Harbor to proxy and cache images from a target public or private registry.

See the [Configure Proxy Cache](https://goharbor.io/docs/latest/administration/configure-proxy-cache/) documentation page.


## S3 storage

Below is an example of the helm values.yaml for using an S3 (object) storage backend like [RustFS](./rustfs):

```yaml
# The persistence is enabled by default and a default StorageClass
# is needed in the k8s cluster to provision volumes dynamically.
# Specify another StorageClass in the "storageClass" or set "existingClaim"
# if you already have existing persistent volumes to use
#
# For storing images and charts, you can also use "azure", "gcs", "s3",
# "swift" or "oss". Set it in the "imageChartStorage" section
persistence:
  # Enable the data persistence or not. Defaults to true
  enabled: true
  persistentVolumeClaim:
    registry:
      # Specify the "storageClass" used to provision the volume. Or the default
      # StorageClass will be used (the default).
      # Set it to "-" to disable dynamic provisioning
      storageClass: "-"
    jobservice:
      jobLog:
        storageClass: "-"
    # If external database is used, the following settings for database will
    # be ignored
    database:
      storageClass: "-"
    redis:
      storageClass: "YOUR_STORAGE_CLASS"
      accessMode: ReadWriteOnce
      size: 1Gi
    trivy:
      storageClass: "YOUR_STORAGE_CLASS"
      # Default is 5Gi, mine instance appears to use around 12Gi
      size: 10Gi

  # Define which storage backend is used for registry to store
  # images and charts. Refer to
  # https://github.com/distribution/distribution/blob/release/2.8/docs/configuration.md#storage
  # for the detail.
  imageChartStorage:
    # Specify whether to disable `redirect` for images and chart storage, for
    # backends which not supported it (such as using minio for `s3` storage type), please disable
    # it. To disable redirects, simply set `disableredirect` to `true` instead.
    # Refer to
    # https://github.com/distribution/distribution/blob/release/2.8/docs/configuration.md#redirect
    # for the detail.
    disableredirect: true

    # Specify the type of storage: "filesystem", "azure", "gcs", "s3", "swift",
    # "oss" and fill the information needed in the corresponding section. The type
    # must be "filesystem" if you want to use persistent volumes for registry
    type: s3
    s3:
      # Bucket name
      bucket: harbor-registry
      # S3 compatible Service Endpoint
      regionendpoint: http://<SERVICE_NAME>.<NAMESPACE>.svc.cluster.local:9000
      # Set an existing secret for S3 accesskey and secretkey
      # keys in the secret should be REGISTRY_STORAGE_S3_ACCESSKEY and REGISTRY_STORAGE_S3_SECRETKEY for registry
      existingSecret: harbor-registry-s3-credentials
      # encrypt: true - Causes 'err.code="blob upload invalid" err.message="blob upload invalid"' errors on image push
      encrypt: false
      secure: true
      v4auth: true
      chunksize: "33554432"  # 32MB

      # Harbor defaults to a lower threshold where it automatically invokes a multipart upload.
      # This has problems with S3 backends see: https://github.com/goharbor/harbor/issues/12317#issuecomment-654193682
      # In my object storage logs: level=error msg="unknown error completing upload: s3aws: InternalError: Io error: Incomplete body: 4194304 bytes were still expected
      #
      # Setting this to 5368709120 (5GB) forces Harbor to upload images less than 5GB as one continuous stream, eliminating chunk-mismatch errors entirely
      multipartcopythresholdsize: "5368709120"  # Sets threshold to 5GB
...
```

## Links & References

* [Harbor - home page](https://goharbor.io/)
* [Harbor Documentation](https://goharbor.io/docs/latest/)
* [Helm Chart for Harbor - GitHub](https://github.com/goharbor/harbor-helm)
* [How to Deploy Harbor Container Registry with Helm](https://oneuptime.com/blog/post/2026-01-17-helm-harbor-container-registry/view#s3-compatible-storage)
* [How to Use Registry Storage Backends with S3 and Azure Blob for Harbor](https://oneuptime.com/blog/post/2026-02-09-harbor-storage-s3-azure/view#storage-performance-optimization)