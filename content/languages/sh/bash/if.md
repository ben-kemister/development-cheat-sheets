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

## To check if a directory exists:

```shell
if [ -d "$DIRECTORY" ]; then
  echo "$DIRECTORY does exist."
fi
```
## To check if a directory does not exist:

```shell
if [ ! -d "$DIRECTORY" ]; then
  echo "$DIRECTORY does not exist."
fi
```

## Check if variable is not a blank String

```shell
if [ ! -z "$MY_VAR"]; then
  echo "The variable 'MY_VAR' is not blank"
fi
```