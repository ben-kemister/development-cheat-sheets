---
title: PowerShell network utilities
tags:
- scripting
- windows
- powershell
- terminal
- utilities
- network
---

This page provides examples about the use of PowerShell's **network utilities**.
<!--more-->

## DNS resolution

To find the DNS server being used by the system use [`ipconfig`](../../operating_systems/windows/ipconfig):
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

To test what is being returned by the DNS server use `nslookup`:
```powershell
nslookup google.com.au 192.168.0.27
```
```text
Server:  UnKnown
Address:  192.168.0.27

Non-authoritative answer:
Name:    google.com.au
Addresses:  2404:6800:4006:809::2003
          142.251.221.67
```

## Find IP/MAC address of remote machine

To display a list of all active IP addresses on the network, including the physical address (MAC address) use `apr -a`.

For example:
```powershell
arp -a
```
Returns:
```text
Interface: 192.168.0.108 --- 0x11
    Internet Address      Physical Address      Type
    192.168.0.1           93-7d-25-xx-xx-xx     dynamic
    192.168.0.21          68-7d-9f-xx-xx-xx     dynamic
    ...
```

## Test-NetConnection

The [Test-NetConnection](https://learn.microsoft.com/en-us/powershell/module/nettcpip/test-netconnection?view=windowsserver2022-ps) 
cmdlet displays diagnostic information for a connection. 
It supports ping test, TCP test, route tracing, and route selection diagnostics. 
Depending on the input parameters, the output can include the DNS lookup results, a list of IP interfaces, IPsec rules, 
route/source address selection results, and/or confirmation of connection establishment.

```powershell
# Test if HTTP port is open
Test-NetConnection google.com -CommonTCPPort "Http"

# Or define a port number
Test-NetConnection google.com -Port 80

ComputerName     : google.com
RemoteAddress    : 142.250.204.14
RemotePort       : 80
InterfaceAlias   : Wi-Fi
SourceAddress    : 192.168.0.XXX
TcpTestSucceeded : True
```
