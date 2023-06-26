---
title: SpringBoot Properties
tags:
- java
- springboot
- properties
---

This page contains information, syntax, and simple code examples, about the use of **properties** in SpringBoot.
<!--more-->

## Adding Sources

You can add property sources to SpringBoot configuration files as follows:

```java
@Configuration
@PropertySource("classpath:foo.properties")
public class PropertiesWithJavaConfig {
    //...
}
```

### Importing additional files

You can also add additional configuration files using command line properties and form existing files.
You can use the `spring.config.import` property within the `application.properties` or `application.yml` file to easily 
include additional files, which as the following features:

* Can add several files or directories
* the files can be loaded either from the classpath or from an external directory
* indicating if the startup process should fail if a file is not found, or if it's an optional file
* importing extensionless files

For example:
```properties
spring.config.import=classpath:additional-application.properties,
classpath:additional-application[.yml],
optional:file:./external.properties,
classpath:additional-application-properties/
```

## Accessing Properties

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

## Links

For more information see:
* [Properties with Spring and Spring Boot](https://www.baeldung.com/properties-with-spring)
* [Inject Arrays and Lists From Spring Properties Files](https://www.baeldung.com/spring-inject-arrays-lists)


