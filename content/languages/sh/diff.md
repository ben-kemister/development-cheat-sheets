---
title: diff
tags:
 - unix
 - linux
---

`diff` is a command-line utility that allows you to compare two files line by line.
<!--more-->
It can also compare the contents of directories or the output from commands.

## Side-by-side

You can use the `-y` or `--side-by-side` options to compare two files side by side, for example:

```shell
diff -y /tmp/test1 /temp/test2
```

## Compare the output of two commands

You can use **process substitution** to pass filenames to `diff` which are the output of the commands, for example:

```shell
diff <(ls old) <(ls new)
```
