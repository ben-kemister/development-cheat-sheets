---
title: Design patterns
tags:
- python
---

This page contains information and simple code examples about some useful design patterns in Python

## Singleton Pattern

A Singleton pattern in python is a design pattern that allows you to create just one instance of a class, throughout the lifetime of a program. Using a singleton pattern has many benefits. A few of them are:

* To limit concurrent access to a shared resource.
* To create a global point of access for a resource.
* To create just one instance of a class, throughout the lifetime of a program.
* Different ways to implement a Singleton:

A singleton pattern can be implemented in three different ways. They are as follows:

* Module-level Singleton
* Classic Singleton
* Borg Singleton

For more information see [Singleton Pattern in Python – A Complete Guide](https://www.geeksforgeeks.org/singleton-pattern-in-python-a-complete-guide/).

### Classic Singleton

```python
class SingletonClass(object):
  def __new__(cls):
    if not hasattr(cls, 'instance'):
      cls.instance = super(SingletonClass, cls).__new__(cls)
    return cls.instance
   
singleton = SingletonClass()
new_singleton = SingletonClass()
 
print(singleton is new_singleton)
 
singleton.singl_variable = "Singleton Variable"
print(new_singleton.singl_variable)
```
