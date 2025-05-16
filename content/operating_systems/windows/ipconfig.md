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

## Flush DNS cache

To flush the DNS cache in Windows (11), open Command Prompt as **Administrator** and use the command `ipconfig /flushdns`. 

This will clear the cached DNS records, which can help resolve/diagnose network connectivity issues.

## Renew a DHCP lease

1. Release the IP address: `ipconfig /release`
2. Renew the IP address: `ipconfig /renew`
3. Verify the new IP address: `ipconfig` - see if your computer has received a new IP address from the DHCP server. 


