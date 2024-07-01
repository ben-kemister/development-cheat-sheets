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

# Certificate file
openssl x509 -enddate -noout -in file.crt
```

## Show certificate details at URL

```shell
echo | openssl s_client -showcerts -servername google.com -connect google.com:443 2>/dev/null
```
```text
CONNECTED(00000003)
---
Certificate chain
 0 s:CN = *.google.com
   i:C = US, O = Google Trust Services, CN = WR2
   a:PKEY: id-ecPublicKey, 256 (bit); sigalg: RSA-SHA256
   v:NotBefore: Jun 13 15:27:14 2024 GMT; NotAfter: Sep  5 15:27:13 2024 GMT
-----BEGIN CERTIFICATE-----
MIIN4zCCDMugAwIBAgIRAJGr9eV0xqbNCoYAPpiuMp8wDQYJKoZIhvcNAQELBQAw
...
```

> This command will also print out all the cerificates in the chain

