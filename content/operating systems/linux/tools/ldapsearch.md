---
title: ldapserch
tags:
 - unix
 - linux
 - ldap
 - active_directory
---


The [ldapsearch](https://linux.die.net/man/1/ldapsearch) Command-Line Tool processes one or more searches in an LDAP 
directory server.
<!--more-->

## Basic Example

```shell
ldapseach -x -H ldap://domaincontroller:389 -D bind-acount -b "DC=demo,DC=com" -W '(sAMAccountName=user1)'
```

> Note that if using a secure connection the connection string is typically ``ldaps://domaincontroller:636``
> 