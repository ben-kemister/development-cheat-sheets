---
title: Variables
tags:
- bash
- variables
- script
---

This page contains examples about the use of bash variables.
<!--more-->

## Check if variable is not a blank String

```shell
if [ ! -z "$MY_VAR"]; then
  echo "The variable 'MY_VAR' is not blank"
fi
```