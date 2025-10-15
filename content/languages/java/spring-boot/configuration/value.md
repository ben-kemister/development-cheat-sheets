---
title: "@Value"
tags:
- java
- springboot
- properties
- configuration
---

The [@Value annotation](https://docs.spring.io/spring-framework/reference/core/beans/annotation-config/value-annotations.html) 
in SpringBoot is used to inject values into fields, methods, or constructor parameters of Spring-managed beans. 
<!--more-->
It facilitates property-driven dependency injection, allowing externalized configuration values to be easily integrated into an application.

## References & Links

* [A Quick Guide to Spring @Value - Baelding](https://www.baeldung.com/spring-value-annotation)
* [Properties with Spring and Spring Boot](https://www.baeldung.com/properties-with-spring)
* [Inject Arrays and Lists From Spring Properties Files](https://www.baeldung.com/spring-inject-arrays-lists)
* [Spring Expression Language (SpEL)](https://docs.spring.io/spring-framework/reference/core/expressions.html)

## Using/Injecting Properties

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

## Default Values

By default, if the property key is not defined Spring will throw an exception. You can change this behaviour so that a
default value is returned if the property is not set.

```java
// Default value of "DEFAULT_VALUE"
@Value("${some.property:DEFAULT_VALUE}")
private String propertyWithDefaultValue;

// Defaults to "" if not set by a property
@Value("${property.not.set:")
private String defaultsToEmtpyString;

// You can even set the value to null using SPEL
// Defaults to null if not set by a property
@Value("${another.property:#{null}}")
private String defaultsToNull;
```


