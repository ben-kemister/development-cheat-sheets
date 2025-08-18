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

## Download a file

To download a binary file with curl use:
```shell
curl -O http://example.com/path/my-file
```
> Note: 
> * You can omit the `-O` flag when dealing with text files
> * You can use `-o <filename>` to change the download file name

## POST Request
The general form of the curl command for making a POST request is as follows:
`curl -X POST [options] [URL]`

    The -X option specifies which HTTP request method will be used when communicating with the remote server.

## Adding Header

You can add http headers to the requests with the `-h <header_value>` syntax. For example to add a `Host` header use:

```shell
curl http://<IP_ADDRESS_OR_HOSTNAME> -H 'Host: foo.bar.com'
```

## Using a proxy

To get curl to use a proxy use the `-x` or `--proxy` arguments using the syntax `--proxy <[protocol://][user:password@]proxyhost[:port]>`,
for example:
```shell
curl --proxy "http://user:password@proxy:8080" https://example.com
```
> Remember to [Percent encode](https://developer.mozilla.org/en-US/docs/Glossary/Percent-encoding) any special characters in your password.

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

## FTP Drives

You can use curl to download and upload files to an FTP drive.

> It should be noted that CURL works best when dealing with single files on a FTP drive.    
> If you need to deal with multiple files take a look at ``wget``.

### Uploading a file

```shell
curl -u <username:password> --upload-file <PATH_TO_FILE> ftp://<FTP_SERVER>/<DIRECTORY>/
```
> Make sure to end the folder name with a slash `/`, otherwise curl will create a file not a folder.

You can also create a destination folder by using the `--ftp-create-dirs` argument, for example:

```shell
curl -u <username:password> --upload-file <PATH_TO_FILE> ftp://<FTP_SERVER>/<DIRECTORY>/<NEW_DIR_TO_CREATE>/ --ftp-create-dirs
```