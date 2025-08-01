---
title: PowerShell network utilities
tags:
- scripting
- windows
- powershell
- terminal
- utilities
- network
- curl
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

## Invoke-WebRequest (curl)

The [Invoke-WebRequest](https://learn.microsoft.com/en-au/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.3)
utility can be used to perform functions similar to `curl`.

### Request with Header

```powershell
Invoke-WebRequest -Uri http://example.com -Headers @{ Host = "example.com" }


StatusCode        : 200
StatusDescription : OK
Content           : <!doctype html>
<html>
<head>
<title>Example Domain</title>

<meta charset="utf-8" />
<meta http-equiv="Content-type" content="text/html; charset=utf-8" />
<meta name="viewport" conten...
RawContent        : HTTP/1.1 200 OK
Age: 338450
Vary: Accept-Encoding
X-Cache: HIT
Accept-Ranges: bytes
Content-Length: 1256
Cache-Control: max-age=604800
Content-Type: text/html; charset=UTF-8
Date: Tue, 23 May ...
Forms             : {}
Headers           : {[Age, 338450], [Vary, Accept-Encoding], [X-Cache, HIT], [Accept-Ranges, bytes]...}
Images            : {}
InputFields       : {}
Links             : {@{innerHTML=More information...; innerText=More information...; outerHTML=<A href="https://www.iana.org/domains/example">More information...</A>; outerText=More information...; tagName=A; href=https://www.iana.org/domains/example}}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 1256
```

### View response (Content)

If you want to see all the content of an `Invoke-WebRequest` response pipe it through the `Select-Object` command. For example:

```powershell
Invoke-WebRequest -Uri http://example.com -Headers @{ Host = "example.com" } `
| Select-Object -Expand Content
```

### POST requests

```powershell
Invoke-WebRequest -Uri http://example.com -Method POST -ContentType "application/json" `
-Body '{ "familyName": "Surname", "givenName" : "Firstname" }' `
| Select-Object -Expand Content
```

### Open the downloaded file

You can use the command `Invoke-Item` or the shortcut version of `ii` to open the file after it has been downloaded.
For example:
```powershell
Invoke-WebRequest -Uri http://example.com -Headers @{ Host = "example.com" } `
| Select-Object -Expand Content > index.html; ii index.html
```