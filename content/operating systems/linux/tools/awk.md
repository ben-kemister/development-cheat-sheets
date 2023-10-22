---
title: awk
tags:
 - unix
 - linux
---

AWK is a domain-specific language designed for text processing and typically used as a data extraction and reporting tool. 
<!--more-->
Like sed and grep, it is a filter, and is a standard feature of most Unix-like operating systems.

## Simple output formatting

```shell
> echo "ONE two" | awk '{ print $1, "Something else", $2 }'
ONE Something else two
```

## Use a different Separator

By default, AWK uses a space as a separator. You can change the separator as follows:

```shell
# Use the colon (:) as the separator
> echo "ONE:two" | awk -F ':' '{ print $1 }'
ONE
```


