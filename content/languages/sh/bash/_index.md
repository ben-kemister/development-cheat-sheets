---
title: Bash
tags:
- bash
- terminal
- shell
- linux
- script
---

[Bash](https://www.gnu.org/software/bash/) is the GNU Project's shell—the Bourne Again SHell.
<!--more-->

## Topic Pages

{{% children sort="title" description="true" %}}

## Process Substitution

Process substitution allows a process's input or output to be referred to using a filename. It takes the form of:

```shell
# Output
<(list)
```

```shell
# Input
>(list)
```

The process _list_ is run asynchronously, ad its input or output appears as a filename. The filename is passed as an 
argument to the current command as the result of the expansion.

The `>(list)` form writes to the file will provide input for `list`.  
The `<(list)` form reads from the argument, using the output from `list`.

Process substitution is supported on systems that support named pipes (FIFOs) or the `/dev/fd` method of naming open files.