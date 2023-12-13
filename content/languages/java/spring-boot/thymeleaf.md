---
title: Thymeleaf
tags:
- java
- springboot
- thymeleaf
---

This page provides examples for the use of [Thymeleaf](https://www.thymeleaf.org/) in Springboot.
<!--more-->
Thymeleaf is a modern server-side Java template engine for both web and standalone environments.

Thymeleaf's main goal is to bring elegant natural templates to your development workflow — 
HTML that can be correctly displayed in browsers and also work as static prototypes, 
allowing for stronger collaboration in development teams.

## Setup

An easy was to get started is to add the `spring-boot-starter-thymeleaf` dependency to your project. 
For example in your `pom.xml` file:

```xml
<project>
    <dependencies>
        ...
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
    </dependencies>
</project>
```

## Configuration

We need to add some basic configuration to use Thymeleaf, below is a simple example of a Thymeleaf configuration class:

```java
@Configuration
@EnableWebMvc
public class ThymeleafConfiguraton {
    
    private ApplicationContext applicationContext;
    
    @Autowired
    public void setApplicationContext(ApplicationContext applicationContext){
        this.applicationContext = applicationContext;
    }
    
    @Bean
    public SpringResourceTemplateResolver templateResolver(){
        SpringResourceTemplateResolver templateResolver = new SpringResourceTemplateResolver();
        templateResolver.setApplicationContext(this.applicationContext);
        // Look for templates in the /templates/ folder on the classpath
        templateResolver.setPrefix("classpath:/templates/");
        templateResolver.setSuffix(".html");
        templateResolver.setTemplateMode(TemplateMode.HTML);
        templateResolver.setCacheable(true);
        return templateResolver;
    }
    
    @Bean
    public SpringTemplateEngine templateEngine(){
        SpringTemplateEngine templateEngine = new SpringTemplateEngine();
        templateEngine.setTemplateResolver(templateResolver());
        templateEngine.setEnableSptingELCompiler(true);
        return templateEngine;
    }
    
    @Bean
    public ThymeleafViewResolver viewResolver(){
        ThymeleafViewResolver viewResolver = new ThymeleafViewResolver();
        viewResolver.setTemplateEngine(templateEngine());
        viewResolver.setOrder(1);
        return viewResolver;
    }
}
```

## Template(s)

With the configuration above Springboot will look for the Thymeleaf templates in the `/templates/` folder on the classpath.
During development time of a maven project this equates to the files stored in `/src/main/resources/templates/`.

### Basic (html) template

Below is an example of a basic thymeleaf template:

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
    <head>
        <title>Thymeleaf Home Page</title>
    </head>
    <body>
        <h1>Welcome to the Thymeleaf homepage</h1>
        <p>
            This page has been rendered via the Thymeleaf templating system.
        </p>
    </body>
</html>
```

### Thymeleaf html attributes

To access Thymeleaf functions and model attributes (read Java POJOs) you use html attributes.

#### if

You can use the `ht:if` html attribute for boolean operations in the template, for example:

```html
<div th:if"${showIfTrue}">
    <p>This div will only be shown if the 'showIfTrue' model attribute is true</p>
</div>
```

#### (for) each

You can loop over a collection of objects in your model using the `ht:each` html attribute, for example:

```html
<tr th:each="message: ${requests.messages}">
    <td th:text="${message.id}"/>
    <td th:text="${message.time}"/>
    <td th:text="${message.body}"/>
</tr>
```

#### inline

You can use the `ht:inline` attribute to ass/inline text with other content in your template, for example:

```html
<h3 ht:inline="text">Responses [[${responses.name}]]</h3>
```

#### id

You can also set the html `id` attribute of a html node (which is handy when also using JavaScripting) using `th:id`
thymeleaf attribute, for example:

```html
<button type="button" ht:id="${responses.name}" onclick="clear_responses(event)">Clear Responses</button>
```

## View Controller

Controllers form the bridge between the incoming request, selection of the template, and the setting of any attributes 
on the model.

Below is an example of a basic controller:

```java
@Controller
@RequestMapping("/rest/thymeleaf")
public class ThymeleafController {
    
    @GetMapping( path = {"", "/"})
    public String home(Model model){
        
        // Set any attributes (read Java POJOs) on the model for use in the thymeleaf template
        model.addAttribute("attributeName", attributeObject);
        
        // The string returned refers to the name of the thymeleaf template
        // In this example this would be the template file: src/main/resources/templates/home.html
        return "home";
    }
}
```

### Redirecting to another view/page

```java
@Controller
@RequestMapping("/rest/thymeleaf")
public class ThymeleafController {
    
    @GetMapping( path = {"/first"})
    public String first(){
        
        // Redirect to /home
        return "redirect:/home";
    }
}
```


## Links

* [baeldung - Introduction to Using Thymeleaf in Spring](https://www.baeldung.com/thymeleaf-in-spring-mvc)
* [Spring - Handling Form Submission](https://spring.io/guides/gs/handling-form-submission/)