---
title: Spring Boot
tags:
- java
- springboot
---

This page contains information, syntax, and simple code examples, about the use of Spring Boot.
<!--more-->

## Properties

You can add property sources to SpringBoot configuration files as follows:

```java
@Configuration
@PropertySource("classpath:foo.properties")
public class PropertiesWithJavaConfig {
    //...
}
```

You can get the value of the property injected in your class variables using the `@Value` annotation for example:

```java
@Value( "${jdbc.url:aDefaultUrl}" )
private String jdbcUrl;
```

You can also inject an array or list as per the following examples:

```properties
arrayOfStrings=Baeldung,dot,com
```

```java
// As an array
@Value("${arrayOfStrings}")
private String[] arrayOfStrings;

// As a List
@Value("#{'${arrayOfStrings}'.split(',')}")
private List<String> listOfStrings;
```

For more information see:
* [Properties with Spring and Spring Boot](https://www.baeldung.com/properties-with-spring)
* [Inject Arrays and Lists From Spring Properties Files](https://www.baeldung.com/spring-inject-arrays-lists)

## Spring Security

### Method Security

Spring Security supports authorization semantics at the method level. [Link to intro/overview](https://www.baeldung.com/spring-security-method-security)

Make sure you have the _spring-security-config_ dependency in your projects classpath:

```xml
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-config</artifactId>
</dependency>
```

Next, to use the security annotations we need to enable global Method Security:

```java
@Configuration
@EnableGlobalMethodSecurity(
        prePostEnabled = true,
        securedEnabled = true,
        jsr250Enabled = true)
public class MethodSecurityConfig
        extends GlobalMethodSecurityConfiguration {
}
```

