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

## Array Length

To get the length of an array in Bash, you can use the `${#array[@]}` syntax. Here's an example:

```shell
arr=('Hello' 'World' '!');

# Get the length of the array
length=${#arr[@]};

echo "The length of the array is: $length";
```
```text
The length of the array is: 3
```
