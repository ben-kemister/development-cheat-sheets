---
title: Marshalling XML in SpringBoot
tags:
- java
- springboot
- xml
- serialisation
- jaxb
- marshalling
- unmarshalling
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

### Manually applying to RestTemplate

In typical cases the creation of a `Jaxb2Marshaller` will get automatically applied to the creation of any `RestTemplate` instances
by SpringBoot.
If this is not working, or you need more control you can apply it manually, then you can do this as follows:
```java
@Configuration
public class ConfigUtil {
 
    /**
     * @return the RetTemplate. This method sets the Message Converter objects to the RestTemplate so that it uses the 
     *         Jaxb2Marshaller for XML un/marshalling
     */
    @Bean(name = "restTemplate")
    public RestTemplate getRestTemplate(MarshallingHttpMessageConverter converter) {
        RestTemplate restTemplate = new RestTemplate();
 
        restTemplate.setMessageConverters(
                List.of(converter, 
                        new FormHttpMessageConverter(), 
                        new StringHttpMessageConverter()));

        return restTemplate;
    }
 
    /**
     * @return MarshallingHttpMessageConverter object which is responsible for XML
     *         marshalling and unMarshalling processes using Jaxb2Marshaller
     */
    @Bean(name = "marshallingHttpMessageConverter")
    public MarshallingHttpMessageConverter getMarshallingHttpMessageConverter(Jaxb2Marshaller jaxb2Marshaller) {
 
        MarshallingHttpMessageConverter marshallingHttpMessageConverter = new MarshallingHttpMessageConverter();
        marshallingHttpMessageConverter.setMarshaller(jaxb2Marshaller);
        marshallingHttpMessageConverter.setUnmarshaller(jaxb2Marshaller);
        return marshallingHttpMessageConverter;
    }

    @Bean(name = "jaxb2Marshaller")
    public Jaxb2Marshaller getJaxb2Marshaller() {
        Jaxb2Marshaller jaxb2Marshaller = new Jaxb2Marshaller();
        // Add all XML classes to be bound
        jaxb2Marshaller.setClassesToBeBound(Customer.class);
        return jaxb2Marshaller;
    }
}
```
