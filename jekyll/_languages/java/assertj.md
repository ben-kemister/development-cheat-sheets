---
title: AssertJ
---

This page contains information, syntax, and simple code examples, about the use of the [AssertJ](https://joel-costigliola.github.io/assertj/) test library.
The [AssertJ](https://joel-costigliola.github.io/assertj/) library is a fluent API to help create readable assertions within you Java unit tests.

## AssertThatThrownBy()

```java
assertThatThrownBy(() -> {
    List<String> list = Arrays.asList("String one", "String two");
    list.get(2);
}).isInstanceOf(IndexOutOfBoundsException.class)
  .hasMessageContaining("Index: 2, Size: 2");
```
