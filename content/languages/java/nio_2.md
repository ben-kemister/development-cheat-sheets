---
title: NIO2
tags:
- java
- development
- files
- paths
---

This page contains information, syntax, and simple code examples, about the use of Java's [NIO2](https://docs.oracle.com/javase/8/docs/technotes/guides/io/index.html) API.

The new API was introduced in Java 7 and provides methods that make working with files and filesystems much easier.

## Current Directory

The Paths API can be used to get the current working directory:

```java
String workingDirectory = Paths.get("")
        .toAbsolutePath()
        .toString();

System.out.println("PWD: " + workingDirectory);
```