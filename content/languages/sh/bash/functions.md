---
title: functions
tags:
 - linux
 - bash
 - functions
---

AWK is a domain-specific language designed for text processing and typically used as a data extraction and reporting tool.
<!--more-->

## Defining a function

```shell
function_name () {
  commands
}

# or alternatively
function function_name () {
  commands
}
```

## Function Arguments

To pass arguments to a function, add the parameters after the function call separated by spaces.

For example:
```shell
function_name argument1 argument2
```

The table below shows some of the options available when working with function arguments.

| Argument | Description |
| --- | --- |
| `$1`,`$2`, etc | Corresponds to the argument's position after the function name |
| `"$@"` | Expands the list to separate strings. For example "$1", $2", etc |
| `"$*"` | Expands the list into a single string, separating parameters with a space. For example "$1 $2" |

## Local variables

You can use `local` to declare a variable which is only used within a function block.

```shell
my_function () {
  local MY_VAR="something"
  echo $MY_VAR
}

# MY_VAR is not accessible outside of the function block
echo $MY_VAR # This will cause an error
```

## Return value

You can use `return` to specify the exit status (a value between 0 and 255) within your function.

```shell
function fun1 () {
  return 34
}

function fun2 () {
  fun1
  local RESULT=$?
  echo "$RESULT"
}
```

> You can also use the return value in boolean logic like `funA || funB` which will only run `funB` if `funA` returns a 
> non-0 value.

