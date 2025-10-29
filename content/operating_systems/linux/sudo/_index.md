---
title: sudo
tags:
- linux
- sudo
---

`sudo` stands for "superuser do" or "substitute user do," a command on Unix-like operating systems like Linux that 
allows a user to run a program with the security privileges of another user, most commonly the superuser or `root`.
<!--more-->
It enables users to perform administrative tasks like installing software or modifying system files by temporarily elevating 
their privileges, requiring them to enter their own password for authentication.

## Sub topic pages

{{% children sort="title" %}}

## Listing permitted commands

You can use the ``sodo -l`` command to list the access rights for the current users.

The output might look like this:

```shell
#> sudo -l
Matching Defaults entries for myuser on myhost:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, !fqdn,
    use_pty

User myuser may run the following commands on myhost:
    (root) NOPASSWD: /usr/bin/systemctl restart docker
    (root) NOPASSWD: /usr/bin/systemctl restart docker.service
```

If you do not have access rights, it asks for password and writes a message:
```shell
#> sudo -l
[sudo] password for myuser:
Sorry, user myuser may not run sudo on myhost.
```
