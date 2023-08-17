---
title: SpringBoot Security
tags:
- java
- springboot
- security
---

This page contains information, syntax, and simple code examples, about **security** in SpringBoot.
<!--more-->

## Spring Security

```xml
<dependency> 
    <groupId>org.springframework.boot</groupId> 
    <artifactId>spring-boot-starter-security</artifactId> 
</dependency> 
```

### Configuration

When Spring Security is added to a project, it will disable access to all the APIs by default. 
You will need to configure Spring Security to allow access to the APIs you want to expose.

```java
@Configuration
public class SecurityConfiguration {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        
        // Allow open access to any APIs residing under /<app_context>/rest/**
        return http.authorizeRequests()
            .antMatchers("/rest/**")
            .permitAll()
            .and()
            .build();
    }
}
```
For more information about configuring security for different URLs see this [Baeldung article](https://www.baeldung.com/spring-security-configuring-urls).

Alternatively if the application is behind a firewall you may prefer to have a configuration that allow unauthenticated 
access to all the endpoints, for example:

```java
import org.springframework.boot.actuate.autoconfigure.security.servlet.EndpointRequest;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.web.SecurityFilterChain;

@Configuration(proxyBeanMethods = false)
public class MySecurityConfiguration {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http.securityMatcher(EndpointRequest.toAnyEndpoint());
        http.authorizeHttpRequests((requests) -> requests.anyRequest().permitAll());
        return http.build();
    }

}
```


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