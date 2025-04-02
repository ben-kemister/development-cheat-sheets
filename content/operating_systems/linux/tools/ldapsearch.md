---
title: ldapsearch
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
ldapsearch -x -H ldap://domaincontroller:389 -D bind-acount -b "DC=demo,DC=com" -W '(sAMAccountName=user1)'
```

> Note that if using a secure connection the connection string is typically ``ldaps://domaincontroller:636``

### Options

Below is information about some of the common options for the `ldapsearch` command:

| Option      | Description                               |
|-------------|-------------------------------------------|
| `-x`        | Simple authentication                     |
| `-H URI`    | LDAP URI                                  |
| `-D binddn` | Bind Distinguished Name (DN)              |
| `-b basedn` | Base DN for the search                    |
| `-W`        | Prompt for Bind password                  |
| `-w passwd` | Bind password (for simple authentication) |

## Search Syntax

### Nested groups

You can use the extended match operator on AD/LDAP systems to find all entities nested under a group.
For example to return the entities nested under the `User Group` use:
```text
(memberOf:1.2.840.113556.1.4.1941:=CN=User Group,OU=Groups,OU=Company,DC=com,DC=au)
```