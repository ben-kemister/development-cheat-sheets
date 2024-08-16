---
title: If (statement)
tags:
- bash
- variables
- files
- script
---

This page contains examples about the use the bash `if` statement.
<!--more-->

## Check Variables

## Is not a blank String

```shell
if [ ! -z "$MY_VAR"]; then
  echo "The variable 'MY_VAR' is not blank"
fi
```

### Is equal to a value

```shell
if [ "$depth" -eq "0" ]; then
   echo "false";
   exit;
fi
```

## Is not equal to a value

```yaml
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
