---
title: IF (statement)
tags:
- bash
- sh
- variables
- files
- script
---

This page contains examples about the use the sh/bash `if` statement.
<!--more-->

## Check Variables

### Test for blank String

```shell
MY_VAR="bar"
if [ -n "$MY_VAR"]; then
  echo "The variable 'MY_VAR' is not null"
fi
```

```shell
foo=
[ -z "$foo" ] && echo "foo is null"
```
```shell
foo=""
[ -z "$foo" ] && echo "foo is null"
```

> Note:    
> `-n` - test that string has length greater than 0.    
> `-z` - test that string has zero length (i.e. `""`)

### Is equal to a value

```shell
if [ "$depth" -eq "0" ]; then
   echo "false";
   exit;
fi
```

### Is not equal to a value

```shell
ret=$?
if [ "$ret" -ne 0 ]; then
        echo "In If"
else
        echo "In Else"
fi
```

## Check Directories

To check if a directory exists:

```shell
if [ -d "$DIRECTORY" ]; then
  echo "$DIRECTORY does exist."
fi
```

To check if a directory does not exist:

```shell
if [ ! -d "$DIRECTORY" ]; then
  echo "$DIRECTORY does not exist."
fi
```
