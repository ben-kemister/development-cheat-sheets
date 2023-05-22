---
title: OpenSSL
tags:
- linux
- ssl
- certificates
---

[OpenSSL](https://www.openssl.org/) is a software library for applications that provide secure communications over computer networks against eavesdropping or need to identify the party at the other end. 
<!--more-->
It is widely used by Internet servers, including the majority of HTTPS websites.

## Check if expired

```shell
# PEM file
openssl x509 -enddate -noout -in file.pem

# Certifcate file
openssl x509 -enddate -noout -in file.crt
```


