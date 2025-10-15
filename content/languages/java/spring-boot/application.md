---
title: SpringBootApplication
tags:
- java
- springboot
---

This page contains information, and simple code examples, about the SpringBootApplication class.
<!--more-->

## Exiting application gracefully

### Using `CommandLineRunner` or `ApplicationRunner`

Create a component that implements either `org.springframework.boot.CommandLineRunner` or `org.springframework.boot.ApplicationRunner`. 
These interfaces provide a run method that will be executed once the Spring `ApplicationContext` is fully loaded. 
This is where the specific task to be performed should be placed.

```java
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.stereotype.Component;
    import org.springframework.boot.SpringApplication;
    import org.springframework.context.ConfigurableApplicationContext;

    @Component
    public class MyTaskRunner implements CommandLineRunner {

        private final ConfigurableApplicationContext context;

        public MyTaskRunner(ConfigurableApplicationContext context) {
            this.context = context;
        }

        @Override
        public void run(String... args) throws Exception {
            System.out.println("Executing my one-time task...");
            // Your specific business logic or data import goes here
            System.out.println("Task completed.");

            // Initiate shutdown after the task is done
            SpringApplication.exit(context, () -> 0); // Exit with code 0 for success
        }
    }
```