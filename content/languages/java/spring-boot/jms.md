---
title: JMS
tags:
- java
- springboot
- jms
- soap
- mq
---

This page contains information, syntax, and simple code examples, about creating (and using) JMS in SpringBoot.
<!--more-->

## Setup

To get started add the `spring-jms` dependency to your project, along with the vendor specific libraries required.
For example for use of IBM's MQ in `pom.xml` file:

```xml
<project>
    <dependencies>
        ...
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jms</artifactId>
        </dependency>
        <dependency>
            <groupId>jakarta.jms</groupId>
            <artifactId>jakarta.jms-api</artifactId>
        </dependency>
        <dependency>
            <groupId>com.ibm.mq</groupId>
            <artifactId>mq-jms-spring-boot-starter</artifactId>
        </dependency>
    </dependencies>
</project>
```

## Configuration

```java
@Configuration
@EnableJms
public class JmSConfiguration {

}
```

### IBM Specific properties

When using the IBM MQ Springboot libraries you need to add some specific properties to you `application.properties` file.

```properties
ibm.mq.conn-name=localhost(1414)
ibm.mq.queue-manager=QM1
ibm.mq.channel=DEV.APP.SVRCONN
ibm.mq.user=app
```

## SOAP over JMS

If you are dealing with SOAP messages over JMS/MQ then you can create a `MessageConverter` which will be used by Spring to 
convert messages when they arrive.

### Configuration

```java
@Configuration
@EnableJms
public class JmSConfiguration {
    
    @Bean
    public JmsToSoapMessageConverter jmsToSoapMessageConverter(SoapMessageFactory soapMessageFactory){
        JmsToSoapMessageConverter messageConverter = new JmsToSoapMessageConverter();
        messageConverter.setSoapMessageFactory(soapMessageFactory);
        return messageConverter;
    }
    
    @Bean
    public MessageFactory soapMessageFactory(){
        try{
            return MessageFactory.newInstance(SOAPConstants.SOAP_1_2_PROTOCOL);
        } catch (SOAPException e){
            throw new RuntimeException(e);
        }
    }
}
```

### MessageConverter

With the message converter looking like:

```java
public class JmsToSoapMessageConverter implements MessageConverter, InitializingBean {
    
    private static final Logger LOGGER = LoggerFactory.getLogger(JmsToSoapMessageConverter.class);
    
    private SoapMessageFactory soapMessageFactory;
    
    public void setSoapMessageFactory(SoapMessageFactory soapMessageFactory){
        this.soapMessageFactory = soapMessageFactory;
    }
    
    @Override
    public void afterPropertiesSet(){
        Preconditions.checkNotNull(soapMessageFactory, "Property 'soapMessageFactory' is required");
    }

    /**
     * Converts from a SoapMessage to a JMS message
     */
    @Override
    public Message toMessage(Object toConvert, Session session) throws JMSException, MessageConversionException {
        
        if(!(toConvert instanceof SoapMessage)){
            throw new MessageConversionException(String.format("Request to convert unsupported object: %s", toConvert));
        }
        
        SoapMessage soapMessage = (SoapMessage) toConvert;
        
        try(ByteArrayOutputStream os = new ByteArrayOutputStream()){
            
            soapMessage.writeTo(os);
            
            String soapString = os.toString(StandardCharsets.UTF_8);
            LOGGER.debug("SOAP Response: {}", soapString);
            
            return session.createTextMessage(soapString);
        } catch (IOException e){
            throw new MessageConversionException("There was an error creating a JMS message from the SOAP Message", e);
        }
    }
    
    @Override
    public SoapMessage fromMessage(Message jmsMessage) throws JMSException, MessageConversionException {
        
        String messageBody = jmsMessage.getBody(String.class);
        LOGGER.debug("SOAP Request: {}", messageBody);
        
        try(InputStream is = new ByteArrayInputStream(messageBody.getBytes(StandardCharsets.UTF_8))){
    
            return soapMessageFactory.createWebServiceMessage(is);
        } catch (IOException e){
            throw new MessageConversionException("There was an error creating a SOAP Message from the JMS message", e);
        }
    }
}
```

### Receiver

Then you can use `SoapMessage` objects directly in your JMS receiver class, such as:

```java
@Component
public class JmsReceiver {

    private SoapMessageFactory soapMessageFactory;

    public void setSoapMessageFactory(SoapMessageFactory soapMessageFactory){
        this.soapMessageFactory = soapMessageFactory;
    }
    
    @JmsListener(destination = "${ibm.mq.request.queue}") // You can also use the queue names directly here
    @SendTo("${ibm.mq.response.queue}")
    public SoapMessage receiveMessage(SoapMessage soapRequest){
        
        if(needToSendFault){
            SoapMessage soapResponse = soapMessageFactory.createWebServiceMessage();
            
            soapResponse.getSoapBody().addClientOrSenderFault("Some error message", Locale.ENGLISH);
            
            return soapResponse;
        }
        
        // Other logic to process and return a SoapMessage
    }
    
}
```