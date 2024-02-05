---
title: "Certificate Stores"
date: 2024-02-05T15:38:11+11:00
tags:
- java
- certificates
- truststore
- keystore
- keytool
---

This page contains information, syntax, and simple examples, about Certificate stores in Java.
<!--more-->

## Using a particular certificate store

You can use JVM flags to direct Java to use a particular trust store, for example:

```text
-Djavax.net.ssl.trustStore=<PATH_TO_FILE>\truststore.p12
-Djavax.net.ssl.trustStorePassword=password
-Djavax.net.ssl.trustStoreType=PKCS12
```

## Keytool

The [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) is a Java CLI tool with manages a 
keystore (database) of cryptographic keys, X.509 certificate chains, and trusted certificates.

It is typically bundled with the JDK and is located in `<JDK_ROOT>\bin\keytool`

## Create a Self-Signed Certificate

Using the keytool CLI tool that is bundled in the JDK, run:

```shell
keytool -genkeypair -alias my-cert -keyalg RSA -keysize 2048 -validity 365 -keystore keystore.p12 -storetype pkcs12 -storepass my_password
```

## Import a certificate

```shell
keytool -im portcert -alias another-cert -file <PATH_TO_FILE>\another-cert.crt -keystore keystore.p12 -storetype pkcs12 -storepass my_password
```

## View the certificates in a store

```shell
keytool -list -keystore keystore.p12 -storetype pkcs12 -storepass my_password
```

## References & Links

* [Introduction to keytool - Baeldung](https://www.baeldung.com/keytool-intro)
* [keytool - Oracle](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html)