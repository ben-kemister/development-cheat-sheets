---
title: "arrays"
tags:
- sh
- bash
- linux
---

Information about the use of arrays in sh/bash.
<!--more-->

## Append to an array

Use `+=` to append to an array:

```shell
arr=('Hello' 'World')
arr+=('!')
echo "${arr[*]}"

# Output:
# Hello World !
```

## Print each element on a new line

```shell
arr=('1' '2'); \
arr+=('3'); \
printf '%s\n' "${arr[@]}"

# Output:
# 1
# 2
# 3
```