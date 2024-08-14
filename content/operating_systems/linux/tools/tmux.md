---
title: tmux
tags:
- terminal
---


[tmux](https://github.com/tmux/tmux/wiki) is an open-source terminal multiplexer for Unix-like operating systems. 
<!--more-->
It allows multiple terminal sessions to be accessed simultaneously in a single window. 
It is useful for running more than one command-line program at the same time.

## Installation

```shell
sudo apt install tmux
```

## Basic usage

| Command | Description |
| --- | --- | 
| `tmux ls` | Lists the active tmux sessions |
| `tmux attach -t 0` | Reattach to the existing tmux session at 0 | 
| `tmux new -s My_Session`| Creates a new tmux session called 'My_Session' |

## KeyBindings
* `Ctrl+B D` — Detach from the current session.


## Links & References

* [Introduction to tmux - Red Hat](https://www.redhat.com/sysadmin/introduction-tmux-linux)
