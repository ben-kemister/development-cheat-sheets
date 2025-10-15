---
title: JUnit
tags:
- java
- testing
- development
---

[JUnit](https://junit.org/) is a widely-used, open-source Java framework for unit testing, allowing developers to write 
and execute automated tests to verify the smallest units of their code, such as individual methods.
<!--more-->
This page contains information, syntax, and simple code examples, about the use of the [JUnit](https://junit.org/) test library.

## @Disabled annotation

The `@Disabled` annotation in JUnit 5 is used to signal that an annotated test class or test method should not be executed during a test run. 
It serves a similar purpose to the `@Ignore` annotation in JUnit 4.

```java
import org.junit.jupiter.api.Disabled;
import org.junit.jupiter.api.Test;

@Disabled("Temporarily disabled due to a known issue with external API")
class MyDisabledTestClass {

    @Test
    void testMethod1() {
        // This test will not run
    }

    @Test
    void testMethod2() {
        // This test will also not run
    }
}
```
