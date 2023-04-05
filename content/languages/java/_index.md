---
title: Java
tags:
- java
---

Java is a general-purpose programming language that is class-based, object-oriented, and designed to have as few implementation dependencies as possible. It is intended to let application developers write once, run anywhere, meaning that compiled Java code can run on all platforms that support Java without the need for recompilation
<!--more-->

## Java Virtual Machine (JVM) options

### Xms and Xmx

The flag `Xmx` specifies the **maximum memory** allocation pool for a Java Virtual Machine (JVM), while `Xms` specifies the **initial memory** allocation pool.

This means that your JVM will be started with Xms amount of memory and will be able to use a maximum of Xmx amount of memory. For example, starting a JVM like below will start it with 256 MB of memory and will allow the process to use up to 2048 MB of memory:

``` cmd
java -Xms256m -Xmx2048m
```
The memory flag can also be specified in different sizes, such as kilobytes, megabytes, and so on.
``` cmd
-Xmx1024k
-Xmx512m
-Xmx8g
```

## Handy methods

### Arrays.asList(array)

Converts an array of objects into a list, great one liner to use when you need to iterate over the array.

## Sub Pages

{{% children sort="title" %}}