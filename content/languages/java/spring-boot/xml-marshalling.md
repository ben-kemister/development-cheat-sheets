---
title: Marshalling XML in SpringBoot
tags:
- java
- springboot
- xml
- serialisation
- jaxb
---

This page contains information, syntax, and simple code examples, about un/marshalling XML in SpringBoot.
<!--more-->

## Using a JAXB runtime

If you have existing POJOs which use Jakarta (nee JAXB) XML annotations you might want to consider using
a JAXB runtime such as glassfish.

My experience is that even with a configured Jackson Jakarta annotation module (`com.fasterxml.jackson.module:jackson-module-jakarta-xmlbind-annotations`) 
the behaviour of each implementation is still different and the XML they produce can be quite different.

To use a JAXB runtime add the following dependencies to your `pom.xml` file:
```xml
<dependency>
    <groupId>jakarta.xml.bind</groupId>
    <artifactId>jakarta.xml.bind-api</artifactId>
</dependency>
<dependency>
    <groupId>org.glassfish.jaxb</groupId>
    <artifactId>jaxb-runtime</artifactId>
</dependency>
```

Then create a configuration which produces a `Jaxb2Marshaller` class, for example:
```java
@Configuration
public class JaxbConfiguration {
    
    @Bean
    public Jaxb2Marshaller jaxb2Marshaller(){
        Jaxb2Marshaller marshaller = new Jaxb2Marshaller();
        // Pretty print the output
        marshaller.setMarshallerProperties(Map.of(Marshaller.JAXB_FORMATTED_OUTPUT, true));
        
        marshaller.setClassesToBeBound(User.class, AnotherXmlClass.class);
        
        return marshaller;
    }
}
```
