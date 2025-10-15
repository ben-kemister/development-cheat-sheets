---
title: Testing
tags:
- java
- springboot
- test
- testing
---

This page contains information, syntax, and simple code examples, about testing in SpringBoot.
<!--more-->

SpringBoot has a heap of **annotations** which can assist with the creation of test code.

## @SpringBootTest

The ``@SpringBootTest`` annotation can be used to bootstrap the full application context.
This means that you can use the `@Autowire` annotation to inject beans from the application context into the test class.

```java
@SpringBootTest
public class EmployeeServiceTest {

    @Autowired
    private EmployeeService employeeService;

    // test code ...
}
```

### Restricting the (application) context

If you don't need the entire application context you can specify the classes within the annotation using;
```java
@SpringBootTest(classes = {MyConfig.class})
@ConfigurationPropertiesScan
public class MyConfigTest {

    @Autowired
    private MyConfig config;

    // test code ...
}
```

> Note: You will need to add the `@ConfigurationPropertiesScan` annotation to either the Test class or the Configuration 
> class to load any external configuration properties (i.e. from an `application.properties` or `application.yaml` file)

## @TestConfiguration

You can use the `@TestConfiguration` annotation to load a particular configuration for a test.

There are two ways of using the annotation. 
Either on a static inner class in the same test class where we want to `@Autowire` the bean;

```java
@SpringBootTest
public class EmployeeServiceTest {

    @TestConfiguration
    static class EmployeeServiceTestConfiguration {
        @Bean
        public EmployeeService employeeService() {
                // test specific logic
                return new EmployeeService() {
            };
        }
    }

    @Autowired
    private EmployeeService employeeService;
}
```

Or you can create a separate test configuration class:

```java
@TestConfiguration
public class EmployeeServiceTestConfiguration {

    @Bean
    public EmployeeService employeeService() {
            // test specific logic
            return new EmployeeService() {
        };
    }
}
```
and then use `@import` on the test classes where you want to use it:

```java
@SpringBootTest
@Import(EmployeeServiceTestConfiguration.class)
public class EmployeeServiceTest {

    @Autowired
    private EmployeeService employeeService;

    // remaining test code
}
```
