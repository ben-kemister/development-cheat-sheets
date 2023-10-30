---
title: SpringBoot Soap WS
tags:
- java
- springboot
- maven
- plugin
- webservice
- soap
- wsdl
- xsd
---

This page contains information, syntax, and simple code examples, about creating (and using) SOAP ws services in SpringBoot.

## Servlet Registration

To be able to register (and expose) the servlet you will need to create a `ServletRegistrationBean`.

```java
@EnableWs
@Configuration
public class CustomWsConfigurerAdapter extends WsConfigurerAdapter {

    @Bean(name = "helloServletRegistration")
    public ServletRegistrationBean<MessaheDisplatcherServlet> helloServletRegistration(ApplicationContext applicationContext) {
        MessageDisplatcherServlet servlet = new MessageDisplatcherServlet();
        servlet.setApplicationContext(applicationContext);
        servlet.setTransformWsdlLocations(true);
        servlet.setTransformSchemaLocations(true);
        // Log the request details
        servlet.setEnableLoggingRequestDetails(true);

        return new ServletRegistrationBean<>(servlet, "/ws/*");
    }
}
```

## Exposing the WSDL

To expose the WSDL file within your application you will need to create a bean as follows:

```java

@EnableWs
@Configuration
public class CustomWsConfigurerAdapter extends WsConfigurerAdapter {

    /**
     * Publishes the WSDL to the path as defined by the bean name
     * e.g. <a href="http://localhost:8080/ws/helloworld.wsdl">http://localhost:8080/ws/helloworld.wsdl</a>
     */
    @Bean(name = "helloworld")
    public Wsdl11Defintion helloWoldWsdlDefinition() {
        SimpleWsdl11Defintition wsdl11Definition = new SimpleWsdl11Defintition();
        wsdl11Definition.setWsdl(new ClassPathResource("/wsdl/HelloWorld/WelloWorld.wsdl"));
        return wsdl11Definition;
    }
}
```

### Exposing an external XSD schema

If you WSDL imports a local schema, this can be published as well using:

```java

@EnableWs
@Configuration
public class CustomWsConfigurerAdapter extends WsConfigurerAdapter {

    /**
     * Publishes the XSD to the path as defined by the bean name
     * e.g. <a href="http://localhost:8080/ws/schema.microsoft.com.Serialization.xsd">http://localhost:8080/ws/schema.microsoft.com.Serialization.xsd</a>
     */
    @Bean(name = "schema.microsoft.com.Serialization")
    public SimpleXsdSchema serializationSchema() {
        return new SimpleXsdSchema(new ClassPathResource("/wsdl/HelloWorld/Serialization.xsd"));
    }
}
```

## SOAP v1.2

Spring defaults to SOAP version 1.1 to be able to use SOAP v1.2 you need to configure the `SaajSoapMessageFactory`.

```java
@EnableWs
@Configuration
public class CustomWsConfigurerAdapter extends WsConfigurerAdapter{

    /**
     * Configure Spring-WS to use SOAP v1.2, rather than the default SOAP 1.1.
     * <br>
     */
    @Bean(name = "soap12MessageFactory")
    public SaajSoapMessageFactory soapMessageFactory(){
        SaajSoapMessageFactory messageFactory = new SaajSoapMessageFactory();
        messageFactory.setSoapVersion(SoapVersion.SOAP_12);
        return messageFactory;
    }

    /**
     * Then use the bean name to set the message factory of your servlet
     */
    @Bean(name = "helloServletRegistration")
    public ServletRegistrationBean<MessaheDisplatcherServlet> helloServletRegistration(ApplicationContext applicationContext){
        MessageDisplatcherServlet servlet = new MessageDisplatcherServlet();
        servlet.setApplicationContext(applicationContext);
        servlet.setTransformWsdlLocations(true);
        servlet.setTransformSchemaLocations(true);
        // Log the request details
        servlet.setEnableLoggingRequestDetails(true);
        // Use a specific message factory bean configured for use with SOAP v1.2
        servlet.setMessageFactoryBeanName("soap12MessageFactory");

        return new ServletRegistrationBean<>(servlet, "/ws/*");
    }
}
```



## Links

* https://www.jetbrains.com/help/idea/generating-xml-schema-from-instance-document.html
* https://cxf.apache.org/docs/maven-cxf-codegen-plugin-wsdl-to-java.html 
* https://codenotfound.com/spring-ws-example.html
* https://spring.io/guides/gs/producing-web-service/

