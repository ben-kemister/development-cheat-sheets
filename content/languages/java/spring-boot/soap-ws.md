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

## SOAP Security

SOAP security is applied in Spring by adding an `Interceptor` to the `EndpointMapping` on the server side, or the `WebServiceGatewaySupport` on the client.

### Server side

```java
@Configuration
public class SoapWsSecurityConfiguration {
 
    @Bean
    public CryptoFactoryBean severCryptoFactoryBean() throws Exception {
        CryptoFactoryBean factoryBean = new CryptoFactoryBean();
        
        // Set the bean to use the servers trust store
        factoryBean.setKeyStoreLocation(new ClassPathResource(serverTruststoreLocation));
        factoryBean.setKeyStorePassword(serverTruststorePassword);
        factoryBean.setKeyStoreType(serverTruststoreType);
        
        return factoryBean;
    }
    
    @Bean("serverWsSecurityInterceptor")
    public Wss4jSecurityInterceptor soapWsSecurityInterceptor() throws Exception {
        Wss4jSecurityInterceptor interceptor = new SoapWsSecurityConfiguration();
        
        // Validate the Timestamp and Signature of the request
        interceptor.setValidationActions("Timestamp Signature");
        interceptor.setValidationTimeToLive(300);
        interceptor.setValidationSignauteCrypto(severCryptoFactoryBean().getObject());
        
        // Add Timestamp to the SOAP security header of the response
        interceptor.setSecurementAction("Timestamp");
        
        // Add a digest to the signature
        interceptor.setSecurementSignatureDigestAlgorithm(WSS4JConstants.SHA1);
        
        return interceptor;
    }
}

@Configuration
public class CustomWsConfigurationSupport extends WsConfigurationSupport {
    
    @Autowired
    @Qualifier("serverWsSecurityInterceptor")
    private Wss4jSecurityInterceptor wss4jSecurityInterceptor;


    /**
     * Apply the Wss4jSecurityInterceptor to the list of interceptors used by the Endpoints in the application
     */
    protected void addInterceptors(List<EndpointInterceptors> interceptors){
        interceptors.add(wss4jSecurityInterceptor);
    }
}
```

### Client side

```java
@Configuration
public class ClientSoapWsSecurityConfiguration {

    @Bean
    public CryptoFactoryBean clientCryptoFactoryBean() throws Exception {
        CryptoFactoryBean factoryBean = new CryptoFactoryBean();

        // Set the bean to use the clients keystore
        factoryBean.setKeyStoreLocation(new ClassPathResource(clientKeyStoreLocation));
        factoryBean.setKeyStorePassword(clientKeyStorePassword);
        factoryBean.setKeyStoreType(clientKeyStoreType);

        return factoryBean;
    }
    
    @Bean("clientWsSecurityInterceptor")
    public Wss4jSecurityInterceptor soapWsSecurityInterceptor() throws Exception {
        Wss4jSecurityInterceptor interceptor = new SoapWsSecurityConfiguration();
        
        /*
            The security configuration of the outgoing request messages
         */
        
        // Needs to match what the server is configured for
        interceptor.setSecurementAction("Timestamp Signature");
        // Set the Keystore which contains the certificate for message signing
        interceptor.setSecurementSignatureCrypto(clientCryptoFactoryBean().getObject());
        
        // The alias of the client certificate to use for the signing
        interceptor.setSecurementSignatureUser(clientCertificateAlias);
        
        // Potential values for the SecurementSignatureKeyIdentifier come from org.apache.wss4j.dom.handler.WSHandlerConstants.WSHandlerConstants.keyIdentifier
        interceptor.setSecurementSignatureKeyIdentifier("DirectReference");
        
        interceptor.setSecurementSignatureAlgorithm(WSS4JConstants.RSA_SHA256);
        
        interceptor.setSecurementSignatureDigestAlgorithm(WSS4JConstants.SHA1);
        
        /*
            The security configuration to validate the servers response message.    
         */
        
        // Validate the Timestamp of the servers response
        interceptor.setValidationActions("Timestamp");
        interceptor.setValidationTimeToLive(300);
        
        return interceptor;
    }
}

@Configuration
public class ClientConfiguration {
    
    @Bean
    public MyClient client(Jaxb2Marshaller jaxb2Marshaller,
                           @Qualifier("soap12MessageFactory") SaajSoapMessageFactory messageFactory,
                           @Qualifier("clientWsSecurityInterceptor") Wss4jSecurityInterceptor wss4jSecurityInterceptor){
        
        // MyClient extends Springs WebServiceGatewaySupport
        MyClient client = new MyClient();
        
        client.setMessageFactory(messageFactory);
        
        // Add the Wss4jSecurityInterceptor to the client
        client.setInterceptors(
                new ClientInterceptor[]{ wss4jSecurityInterceptor } );
        
        client.setMarshaller(jaxb2Marshaller);
        client.setUnmarshaller(jaxb2Marshaller);
        
        return client;
    }
}
```

## Links

* https://www.jetbrains.com/help/idea/generating-xml-schema-from-instance-document.html
* https://cxf.apache.org/docs/maven-cxf-codegen-plugin-wsdl-to-java.html 
* https://codenotfound.com/spring-ws-example.html
* https://spring.io/guides/gs/producing-web-service/

