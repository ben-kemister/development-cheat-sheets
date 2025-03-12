---
title: sed
tags:
 - unix
 - linux
---

`sed` is a stream editor. A stream editor is used to perform basic text transformations on an input stream (a file or input from a pipeline).
<!--more-->
`sed` works by making only one pass over the input(s), and is very efficient. 
sed’s ability to filter text in a pipeline particularly distinguishes it from other types of editors.

## Removing trailing whitespaces

You can remove trailing whitespaces with the following `sed` command:

```shell
sed -i -E 's/[[:space:]]*$// FILE'
```

## Removing Spaced before a newline character

```shell
sed -i -E 's/[[:space:]]+$//g' file.ext
```