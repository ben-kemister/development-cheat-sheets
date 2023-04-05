---
title: Python
tags:
- python
- development
---

[Python](https://www.python.org/) is a high-level, interpreted, general-purpose programming language. Its design philosophy emphasizes code readability with the use of significant indentation. Python is dynamically-typed and garbage-collected. It supports multiple programming paradigms, including structured, object-oriented and functional programming.
<!--more-->

## Python IDEs

Two popular IDEs for python are Visual Studio and [PyCharm](https://www.jetbrains.com/pycharm/).

Personally I prefer PyCharm as it has a very similar layout and keybindings to [Intellij](https://www.jetbrains.com/idea/).

## Python data types

Python has the following data types built-in by default, in these categories:

* Text Type: `str`
* Numeric Types: `int`, `float`, `complex`
* Sequence Types: `list`, `tuple`, `range`
* Mapping Type: `dict`
* Set Types: `set`, `frozenset`
* Boolean Type: `bool`
* Binary Types: `bytes`, `bytearray`, `memoryview`
* None Type: `NoneType`

## Program flow control

### If Else statements

```python
a = 200
b = 33
if b > a:
  print("b is greater than a")
elif a == b:
  print("a and b are equal")
else:
  print("a is greater than b")
```

## Sub Pages

{{% children sort="title" description="true" %}}