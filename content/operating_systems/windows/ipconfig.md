---
title: ipconfig
tags:
- windows
- terminal
- ip
- dns
---

This page contains information about the use of the `ipconfig` in Windows.
<!--more-->

## Identify DNS Sever

To find the DNS server being used by the system use `ipconfig /all`:
```powershell
ipconfig /all
```
```text
...
Ethernet adapter Ethernet 2:

   Connection-specific DNS Suffix  . : lan
   ...
   DNS Servers . . . . . . . . . . . : 192.168.0.27
                                       192.168.0.22
```

## Flush DNS cache

To flush the DNS cache in Windows (11), open Command Prompt as **Administrator** and use the command `ipconfig /flushdns`. 

This will clear the cached DNS records, which can help resolve/diagnose network connectivity issues.

## Renew a DHCP lease

1. Release the IP address: `ipconfig /release`
2. Renew the IP address: `ipconfig /renew`
3. Verify the new IP address: `ipconfig` - see if your computer has received a new IP address from the DHCP server. 


