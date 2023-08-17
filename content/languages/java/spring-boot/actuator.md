---
title: SpringBoot Actuators
tags:
- java
- springboot
- actuator
- webservices
- health
- monitoring
---

This page contains information, syntax, and simple code examples, about [Springboots' actuator](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html).

Springboots' actuator adds production-ready features to our application.
When you add the actuator dependency functions such as monitoring, gathering metrics, understanding traffic, or the state of ths database becomes much much easier.

## Adding Actuator

To enable Spring Boot Actuator, just need add the `spring-boot-actuator` dependency to your project.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

## Endpoints

There are a heap of [endpoints](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html#actuator.endpoints) available,
but by default only the `/health` endpoint is exposed.

For the actuator endpoints you can:
* [Enable/disable](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html#actuator.endpoints.enabling) them - which removes them from the application context
* [Expose/hide](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html#actuator.endpoints.exposing) them over HTTP or JMX
* [Secure](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html#actuator.endpoints.security) them - only the `/health` endpoint is unsecured over HTTP by default.

## Exposing

If you deploy applications behind a firewall, it may be easier to expose all your actuator endpoints can be accessed without requiring authentication. 
You can do so by changing the management.endpoints.web.exposure.include property, as follows:

```properties
management.endpoints.web.exposure.include=*
```

## Security

If Spring Security is present, you would need to also need add custom security configuration that allows unauthenticated access to the endpoints.
You can do this by adding an entry for `/actuator/**` for example:

```java
@Bean
public SecurityWebFilterChain securityWebFilterChain(ServerHttpSecurity http) {
    return http.authorizeExchange()
        .pathMatchers("/actuator/**").permitAll()
        .anyExchange().authenticated()
        .and().build();
}
```

## Links

* [Baeldung: Spring Boot Actuator](https://www.baeldung.com/spring-boot-actuators)
* [Spring: Production-ready Features](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html#actuator)

