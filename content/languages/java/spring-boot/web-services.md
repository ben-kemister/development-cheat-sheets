---
title: SpringBoot Web Services
tags:
- java
- springboot
- web
- webservices
---

This page contains information, syntax, and simple code examples, about **Web Services** in SpringBoot.
<!--more-->

## JAX-RS vs Spring MVC for REST API development

**JAX-RS** hinges on providing a set of Java Annotations and applying them to plain Java objects. 
The annotations help us to abstract the low-level details of the client-server communication. 
To simplify their implementations, it offers annotations to handle HTTP requests and responses and bind them in the code. 
JAX-RS is **only a specification** and needs a compatible implementation to be used.

**Spring MVC** is a **complete framework** with REST capabilities. 
Like JAX-RS, it also provides us with useful annotations to abstract from low-level details. 
Its main advantage is being a part of the Spring Framework ecosystem. So it works with Spring dependency injection
and the other components like Spring AOP, Spring Data REST, and Spring Security.

References:
* [REST API: JAX-RS vs Spring](https://www.baeldung.com/rest-api-jax-rs-vs-spring)

## Root context

The root context for the application can be defined using:

```properties
server.servlet.context-path=/some-path
```

## Error Handling

* [Error Handling for REST with Spring](https://www.baeldung.com/exception-handling-for-rest-with-spring)


### Global exception handling

You can use the ``@ControllerAdvice`` annotation and extend the `ResponseEntityExceptionHandler` class to define how you
want to handle certain errors for example:

```java
@ControllerAdvice
public class RestResponseEntityExceptionHandler extends ResponseEntityExceptionHandler {

    @ExceptionHandler({ MyCustomApplicationException.class })
    public ResponseEntity<Object> handleCustomException(Exception ex, WebRequest request) {
        return new ResponseEntity<Object>(
          "Custom Error message here", new HttpHeaders(), HttpStatus.BAD_REQUEST);
    }
    ...
}
```

## Get all endpoints

There are many ways to achieve this, [as discussed here](https://www.baeldung.com/spring-boot-get-all-endpoints). 
But if you want to do this programmatically then you can use the `@EventListener` annotation to retrieve the `ApplicationContext`
from the `ContextRefreshedEvent` as follows:
```java
@EventListener
public void handleContextRefresh(ContextRefreshedEvent event) {
    ApplicationContext applicationContext = event.getApplicationContext();
    RequestMappingHandlerMapping requestMappingHandlerMapping = applicationContext
                                                                    .getBean("requestMappingHandlerMapping", RequestMappingHandlerMapping.class);
    Map<RequestMappingInfo, HandlerMethod> map = requestMappingHandlerMapping.getHandlerMethods();
    map.forEach((key, value) -> LOGGER.info("{} {}", key, value));
}
```

## Get Servlet context path

```java
@Component
public class SpringBean {

    @Autowired
    private ServletContext servletContext;

    @PostConstruct
    public void showIt() {
        System.out.println(servletContext.getContextPath());
    }
}
```
or via the properties using:
```java
@Component
public class SpringBean {

    @Value("#{servletContext.contextPath}")
    private String servletContextPath;
}
```
