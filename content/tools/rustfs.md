---
title: RustFS
tags:
- s3
- object_storage
---

The [RustFS](https://rustfs.com/) project offers high-performance, S3-compatible distributed object storage built on Rust, 
aiming to combine the simplicity of MinIO with Rust's memory safety and performance.
<!--more-->

## Architecture and Features

RustFS is a distributed object storage system that provides S3 compatibility, making it suitable for use in data lakes, 
AI, and big data applications. The core library `rustfs/crates/config` is a configuration management and validation module for the distributed storage system.

Key features of the configuration module include support for multiple configuration formats (TOML, YAML, JSON, ENV), 
environment variable integration with override capabilities, configuration validation, type safety, hot-reloading for dynamic updates, 
and secure credential handling.

## Deployment and Configuration (Kubernetes)

RustFS can be deployed on Kubernetes via Helm charts, supporting both standalone and distributed operational modes.

* **Standalone Mode:** Utilizes a single pod and single PVC.
* **Distributed Mode:** Supports multiple configurations:
    *   4 pods with 4 PVC each (default).
    *   16 pods with 1 PVC each (using `--set replicaCount="16"`).

## Installation and Operational Details

* **Prerequisites:** Helm V3 and RustFS >= 1.0.0-alpha.69.
* **Ingress Configuration:** Deployment requires specifying an ingress class, either `traefik` or `nginx`.
* **Access:** The cluster can be accessed via HTTPS using the default credentials `rustfsadmin`.
* **TLS Configuration:** To enable TLS (recommended for production), users must generate or acquire certificate and key files (`tls.crt` and `tls.key`) 
    and specify them during Helm installation using the `--set-file` parameter.

## Configuration Management

RustFS relies heavily on environment variables following a flat `RUSTFS_*` naming convention for top-level configuration.

* **Naming Conventions:** Environment variables like `RUSTFS_REGION` and `RUSTFS_ADDRESS` are used.
* **CORS:** `RUSTFS_CORS_ALLOWED_ORIGINS` controls generic CORS headers, which defaults to empty unless configured.
* **Health Checks:** A range of variables (`RUSTFS_HEALTH_ENDPOINT_ENABLE`, `RUSTFS_HEALTH_MINIMAL_RESPONSE_ENABLE`, etc.) 
    manage the exposure and behavior of health endpoints, including busy protection.
* **Timeout Policies:** Operation-specific timeouts can be set using `RUSTFS_DRIVE_*_TIMEOUT_SECS`. 
    The overall timeout policy is defined by `RUSTFS_DRIVE_TIMEOUT_PROFILE`, which can be set to `default` or `high_latency`.
* **Startup Boundary:** `RUSTFS_UNSUPPORTED_FS_POLICY` controls the startup behavior when the detected local endpoint 
    filesystem is outside supported production boundaries (POSIX filesystems are supported; network-mounted filesystems are not).

## Further Information

* [RustFS GitHub Repository](https://github.com/rustfs/rustfs) - For complete documentation, examples, and usage guides, visit the main RustFS repository.
* [RustFS Helm Charts](https://charts.rustfs.com/) - For deployment details and parameter schemas, consult the RustFS Helm Charts page.
* [RustFS Config - Configuration Management](https://github.com/rustfs/rustfs/blob/main/crates/config/README.md) - Configuration management and validation module for RustFS distributed object storage

