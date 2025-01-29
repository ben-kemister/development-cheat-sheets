---
title: (TLS) Certificates
tags:
- certificates
- tls
- https
- ca
---

Transport Layer Security (TLS) certificates, also known as Secure Sockets Layer (SSL), 
are essential to securing internet browser connections and transactions through data encryption.
<!--more-->

## File Extensions

The file extensions used in certificate systems can be _very_ loosely seen as a type system.

`.pem` stands for Privacy Enhanced Mail (PEM); it simply indicates a base64 encoding with header and footer lines. 
Mail traditionally only handles text, not binary which most cryptographic data is, so some kind of encoding is required to make the contents part of a mail message itself (rather than an encoded attachment). 
The contents of the PEM are detailed in the header and footer line - `.pem` itself doesn't specify a data type - just like `.xml` and `.html` do not specify the contents of a file, they just specify a specific encoding;

`.key` can be any kind of key, but usually it is the private key - OpenSSL can wrap private keys for all algorithms (RSA, DSA, EC) in a generic and standard PKCS#8 structure, 
but it also supports a separate 'legacy' structure for each algorithm, and both are still widely used even though the documentation has marked PKCS#8 as superior for almost 20 years; 
both can be stored as DER (binary) or PEM encoded, and both PEM and PKCS#8 DER can protect the key with password-based encryption or be left unencrypted;

`.csr` or `.req` or sometimes `.p10` stands for Certificate Signing Request as defined in PKCS#10; 
it contains information such as the public key and common name required by a Certificate Authority to create and sign a certificate for the requester, 
the encoding could be PEM or DER (which is a binary encoding of an ASN.1 specified structure);

`.crt` or `.cer` files are simply certificates. Usually a X509v3 certificate, but the encoding could be PEM or DER; 
A certificate contains the public key, but it contains much more information (most importantly the signature by the Certificate Authority over the data and public key, of course).