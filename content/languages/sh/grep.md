---
title: grep
tags:
 - unix
 - linux
---

grep is a command-line utility for searching plain-text data sets for lines that match a regular expression. 
<!--more-->
Its name comes from the ed command `g/re/p`, which has the same effect. 
grep was originally developed for the Unix operating system, but later available for all Unix-like systems and some others such as OS-9.

## Ignore case

To make the grep search case-insensitive (i.e. ignore the case) ise the `-i` or `--ignore-case` flags.
For example:

```shell
$ echo -e "hello\nHELLO" | grep -i hellO
hello
HELLO
```

## OR operator

There are 2 ways that you can do OR operations with grep.

1. You can use `\|` to separate multiple patterns for example: `grep 'pattern1\|pattern2' filename`
2. Alternately you can use grep with the `-E` (extended regexp mode) for example: `grep -E 'pattern1|pattern2'`

