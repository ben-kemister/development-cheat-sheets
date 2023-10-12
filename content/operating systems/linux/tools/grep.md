---
title: Grep
tags:
 - unix
 - linux
---

grep is a command-line utility for searching plain-text data sets for lines that match a regular expression. 
<!--more-->
Its name comes from the ed command `g/re/p`, which has the same effect. 
grep was originally developed for the Unix operating system, but later available for all Unix-like systems and some others such as OS-9.

## OR operator

There are 2 ways that you can do OR operations with grep.

1. You can use `\|` to separate multiple patterns for example: `grep 'pattern1\|pattern2' filename`
2. Alternately you can use grep with the `-E` (extended regexp mode) for example: `grep -E 'pattern1|pattern2'`

