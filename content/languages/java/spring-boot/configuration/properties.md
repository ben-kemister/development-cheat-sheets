---
title: Properties
tags:
- java
- springboot
- properties
---

This page contains information, syntax, and simple code examples, about the use of SpringBoot **properties**.
<!--more-->

## Supplying runtime property files

You can select `*.properties` files (including files not on the classpath) to use at application runtime by adding the
`spring.config.location` environment property (a comma-separated list of directory locations, or file paths) to the arguments
when launching the application.

```shell
# For specific files on the classpath
$ java -jar myproject.jar --spring.config.location=classpath:/default.properties,classpath:/override.properties
# or for an external set of file
$ java -jar app.jar --spring.config.location=config/*/
```

> Note that by setting `spring.config.location` Spring will use this file as the default property source.

### Importing additional files

#### From the command line

If you just want to **add** to the default property source you can use `spring.config.additional-location` instead.     
For example the following will also load the properties from `./my-dir/more.properties`:

```shell
java -jar myproject.jar --spring.config.additional-location=./my-dir/more.properties
```

#### From other properties files

You can also add **additional configuration files** using command line properties and form existing files.
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

## Programmatically Adding Sources

You can add property sources to SpringBoot configuration files as follows:

```java
@Configuration
@PropertySource("classpath:foo.properties")
public class PropertiesWithJavaConfig {
    //...
}
```

## Property Resolution

Spring's `PropertySourcesPlaceholderConfigurer` class resolves `${…}` placeholders within bean definition property 
values and `@Value` annotations.

> If you are having issues where the `${…}` is not being substituted with the property value, make sure  
> `PropertySourcesPlaceholderConfigurer` is in the Spring context.

## Using Environmental Variables

You can use the syntax `${MY_ENVIRONMENT_VARIABLE}` within SpringBoot properties files. 
SpringBoot will source these values from the systems' environmental variables, for example: 

```yaml
app:
  database:
    # The password will be sourced from the 'DB_PASSWORD' environmental variable
    password: ${DB_PASSWORD}
```

## Links

For more information see:
* [Properties with Spring and Spring Boot](https://www.baeldung.com/properties-with-spring)
* [Inject Arrays and Lists From Spring Properties Files](https://www.baeldung.com/spring-inject-arrays-lists)
* [Spring Expression Language (SpEL)](https://docs.spring.io/spring-framework/reference/core/expressions.html)


