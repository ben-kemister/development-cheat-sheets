---
title: WSDL
tags: 
 - development
 - xml
 - xsd
 - wsdl
 - soap
 - webservice
---

WSDL is an XML notation for describing a web service. 
<!--more-->
A WSDL definition tells a client how to compose a web service request and describes the interface that is provided by the web service provider.

## Importing local wsdl files

You can import local wsdl files into another wsdl, for example:

```xml
<wsdl:import namespace="http://com.example/Utility" location="./utility.wsdl" />
```

## Importing local schema files

You can import local schema (.xsd) files for use in your wsdl, for example:

```xml
<wsdl:types>
    <xsd:schema targetNamespace="http://com.example/Imports">
        <xsd:import schemaLocation="./local.schema.xsd" namespace="http://com.example/Something" />
    </xsd:schema>
</wsdl:types>
```


