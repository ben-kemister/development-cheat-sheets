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

## Search Syntax

### Nested groups

You can use the extended match operator on AD/LDAP systems to find all entities nested under a group.
For example to return the entities nested under the `User Group` use:
```text
(memberOf:1.2.840.113556.1.4.1941:=CN=User Group,OU=Groups,OU=Company,DC=com,DC=au)
```