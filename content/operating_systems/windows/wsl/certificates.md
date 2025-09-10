---
title: WSL Certificates
tags:
- windows
- wsl
- certificates
---

This page contains information about dealing with SSL certificates in WSL.
<!--more-->

## Adding a Certificate to the trust store

To add a certificate to a trust store in a Ubuntu WSL machine:

1. Copy the certificate (in PEM) format to: ``/user/local/share/ca-certificates/``
   > Make sure the certificate has a file extension of `.crt`
2. Add the certificate to the trust store with:
   ```shell
   [sudo] update-ca-certificates
   ```
3. (Optional) To check if the certificate has been added to the trust store you can look in the `/etc/ssl/certs/` directory:
    ```shell
    sudo ls -la /etc/ssl/certs/ | grep my-ca-cert
    ```