---
title: tr
tags:
 - unix
 - linux
---

[tr](https://man7.org/linux/man-pages/man1/tr.1.html) is short for “translate”. 
<!--more-->
It is a member of the GNU coreutils package and therefore, it’s available in all Linux distros.

## Replace space with new lines

```shell
$ echo "one two" | tr ' ' '\n'
one
two
```

## Remove all whitespaces

```shell
$ echo " one two three 4" | tr -d " \t\n\r"
onetwothree4
```

## Handy links

* [Linux tr Command - Baeldung](https://www.baeldung.com/linux/tr-command)
