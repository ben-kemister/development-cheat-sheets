---
title: DNS
tags:
- network
- dns
---

The [Domain Name System (DNS)](https://en.wikipedia.org/wiki/Domain_Name_System) is a hierarchical and distributed name 
service that provides a naming system for computers, services, and other resources on the Internet or other 
Internet Protocol (IP) networks. 
<!--more-->
It associates various information with domain names (identification strings) assigned to each of the associated entities. 
Most prominently, it translates readily memorized domain names to the numerical IP addresses needed for locating and 
identifying computer services and devices with the underlying network protocols. 
The Domain Name System has been an essential component of the functionality of the Internet since 1985.

## DNS Record Types

| Record | Description                                                                                                                                                                 |
|--------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------| 
| A      | Indicates the IP address of a given domain                                                                                                                                  |
| CNAME  | A CNAME (Canonical Name) record is a type of DNS record that maps an alias name to a true or canonical domain name                                                          |
| NS     | NS stands for 'nameserver,' and the nameserver record indicates which DNS server is authoritative for that domain <br> (i.e. which server contains the actual DNS records). |
| DS     | The DNSKEY and DS records are used by DNSSEC resolvers to verify the authenticity of DNS records.                                                                           |
| RRSIG  | An RRSIG-record holds a DNSSEC signature for a record set (one or more DNS records with the same name and type).                                                            |
| NSEC3  | An NSEC3 (next secure record version 3) links to the next record name in the zone (in hashed name sorting order) <br> and lists the record types that exist for the name    |

