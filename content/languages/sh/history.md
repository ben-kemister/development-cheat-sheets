---
title: history
tags:
- linux
- terminal
---

The Linux `history` command displays a numbered list of previously executed commands, 
<!--more-->
which are stored in a file (commonly `~/.bash_history` for the Bash shell). 
It allows users to review, reuse, and manage past commands efficiently

## History for other users

To view the command history for other users you will need to access the `~/.bash_history` file for that user.

For example, to view the history file for a user called USER_A:
```shell
tail /home/USER_A/.bash_history
```
