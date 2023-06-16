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