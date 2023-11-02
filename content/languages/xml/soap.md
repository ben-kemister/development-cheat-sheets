---
title: SOAP
tags: 
 - xml
 - soap
---

Simple Object Access Protocol (SOAP) is a messaging protocol specification for exchanging structured information 
in the implementation of web services in computer networks.
<!--more-->
It uses XML Information Set for its message format, and relies on application layer protocols, 
most often Hypertext Transfer Protocol (HTTP), although some legacy systems communicate over Simple Mail Transfer Protocol (SMTP), 
for message negotiation and transmission.

## Identify SOAP Version

### Using Namespaces

You can identify the SOAP version by looking at the namespaces in the message:

* SOAP v1.1: http://schemas.xmlsoap.org/soap/envelope/
* SOAP v1.2: http://www.w3.org/2003/05/soap-envelope

Below is the sample xml message which has the envelope http://schemas.xmlsoap.org/soap/envelope/ which is SOAP v1.1.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <soapenv:Body>
        <listUsers xmlns="http://examples.com/" />
    </soapenv:Body>
</soapenv:Envelope>
```

### SOAP Action Header

Typically, SOAP 1.1 will have the content type as `text/xml`. Below is the sample header information.

```text
POST /YourService HTTP/1.1
Content-Type: text/xml; charset="utf-8"
Content-Length: xxx
SOAPAction: "urn:uuid:youraction"
```
But SOAP 1.2 will have the content type as `application/soap+xml`.

