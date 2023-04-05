---
title: 'Using OAuth to secure resource servers'
draft: true
tags:
 - oauth
 - java
 - springboot
 - javascript
 - cors
 - csrf
---

This post contains information about using OAuth to secure a SpringBoot resource servers.
<!--more-->

## Definitions

_Cross Origin Resource Sharing_ (CORS) is when the host that serves the (javascript) client is different from the host that serves the data, 
in this case CORS enables cross-domain communication.

A _resource server_ is a server which provides access to some content/resources

## Configuring SpringBoot

### Global CORS Configuration

```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry
                // Enables cross-origin request handling for the specific pattern
                .addMapping("/admin")
                // Stes the HTTP methods to allow
                // By default 'simple' methods GET, HEAD, and POST are allowed
                .allowedMethods("GET", "POST", "PUT", "PATCH", "DELETE")
                // Needed to set the `Access-Control-Allow-Credentials` header to true
                // This is needed to comply with the CORS policy on modern Web Browsers
                .allowCredentials(true);
    }
}
```

## References

* [CORS with Spring](https://www.baeldung.com/spring-cors)
* [Fixing 401s with CORS Preflights and Spring Security](https://www.baeldung.com/spring-security-cors-preflight)
* [Access-Control-Allow-Credentials](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Credentials)




