---
title: Collections
tags:
- java
- list
- array
---

This page contains information, syntax, and simple code examples, about the use of collections in Java.

## Arrays

### Arrays.asList(array)

Converts an array of objects into a list, great one-liner to use when you need to iterate over the array.

```java
// Great way to create a collection within one line
List<String> strings = Arrays.asList("one", "two", "three");
```

## Sets

### Copying/Cloning Sets

#### Copy Constructor

One way of copying a Set is to use the copy constructor of a Set implementation. 
The copy constructor is a special type of constructor that is used to create a new object by copying an existing object.

```java
Set<T> copy = new HashSet<>(original);
```

> Note we are not really cloning the elements of the given set. 
> We’re just copying the object references into the new set. 
> For that reason, each change made in one element will affect both sets. 

#### Java 10

Java 10 adds a new method to the Set interface that allows us to create an immutable set from the elements of a given collection:

```java
Set<T> copy = Set.copyOf(original);
```
> Note that `Set.copyOf` expects a non-null parameter.

