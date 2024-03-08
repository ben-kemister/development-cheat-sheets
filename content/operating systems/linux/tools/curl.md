---
title: "cURL"
tags:
- linux
- terminal
---

[cURL](https://curl.se/) is a computer software project providing a library and command-line tool for transferring data 
using various network protocols. 
<!--more-->
The name stands for "Client for URL"

## POST Request
The general form of the curl command for making a POST request is as follows:
`curl -X POST [options] [URL]`

    The -X option specifies which HTTP request method will be used when communicating with the remote server.

## Adding Header

You can add http headers to the requests with the `-h <header_value>` syntax. For example to add a `Host` header use:

```shell
curl http://<IP_ADDRESS_OR_HOSTNAME> -H 'Host: foo.bar.com'
```

## Check http basic-auth

You can use curl to check if basic-auth is working as expected for example:

```shell
curl http://<IP_ADDRESS_OR_HOST_FQDN>/ \
-u 'wronguser:wrongpass' \
-s -w"%{http_code}" -o /dev/null
```
```text
401
```

```shell
curl https://<IP_ADDRESS_OR_HOST_FQDN>/ \
-u 'valid_user:valid_password' \
-s -w"%{http_code}" -o /dev/null
```
```text
200
```
