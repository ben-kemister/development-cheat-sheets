---
title: Apache HTTP server (httpd)
tags: 
- apache
- http
- httpd
- tomcat
---

The [Apache HTTP Server](https://httpd.apache.org/) is a free and open-source cross-platform web server software, released under the terms of Apache License 2.0.
<!--more-->

## Using Environmental variables in *.conf files

You can use the `${VARIABLE_NAME}` syntax to use an environmental variables within `*.conf` files.

For example:

```text
ProxyPass /${SOME_PATH}/ ${TOMCAT_URL}
```

## Proxying a Tomcat backend 

To connect Apache httpd to a Tomcat backend you first need to load some additional modules:

```text
LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_http_module modules/mod_proxy_http.so
```
Then you need to define which path(s) are going to be proxied. You do this by adding the following line to your configuration: 

```text
# Make sure that you end both parameters with a slash '/'
ProxyPass /my-path/ http://localhost:8080/app_context/
```

## Loading config files/directories if they exist

You can use the `<IfFile [FILENAME]>` directive around the `Include` directive to only load the file(s) if they exist.
For example:

```text
<IfFile conf/conf-more.d/*.conf>
    Include conf/conf-more.d/*.conf
</IfFile>
```