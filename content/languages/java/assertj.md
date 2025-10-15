---
title: AssertJ
tags:
- java
- testing
- development
---

This page contains information, syntax, and simple code examples, about the use of the [AssertJ](https://joel-costigliola.github.io/assertj/) test library.
<!--more-->
The [AssertJ](https://joel-costigliola.github.io/assertj/) library is a fluent API to help create readable assertions within you Java unit tests.

## Testing thrown exceptions - AssertThatThrownBy()

```java
import static org.assertj.core.api.Assertions.assertThatThrownBy;

@Test
void test_methodShouldThrowException(){
    
    assertThatThrownBy(() -> {
        List<String> list = Arrays.asList("String one", "String two");
        list.get(2);
    }).isInstanceOf(IndexOutOfBoundsException.class)
      .hasMessageContaining("Index: 2, Size: 2");
}

```

## Testing methods that don't return a value

```java
import static org.assertj.core.api.Assertions.assertThatCode;

@Test
void test_methodWithNoReturnValue(){
    assertThatCode(() -> doWork())
        .doesNotThrowAnyException();
}
```