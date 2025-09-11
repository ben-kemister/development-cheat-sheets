---
title: HTTP Requests
tags:
- scripting
- windows
- powershell
- network
- curl
---

This page provides examples about the use of PowerShell's HTTP request functions.
<!--more-->

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

## Invoke-WebRequest (Windows curl)

The [Invoke-WebRequest](https://learn.microsoft.com/en-au/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.3)
utility can be used to perform functions similar to `curl`.

### Downloading a file

You can use the following to download a file with `Invoke-WebRequest`:
```powershell
Invoke-WebRequest -Uri "https://example.com/path/to/file.zip" -OutFile "C:\Downloads\file.zip"
```
> Note if you are dealing with larger files, you are better off using `Start-BitsTransfer`.

#### Open the downloaded file

You can use the command `Invoke-Item` or the shortcut version of `ii` to open the file after it has been downloaded.
For example:
```powershell
Invoke-WebRequest -Uri http://example.com -Headers @{ Host = "example.com" } `
| Select-Object -Expand Content > index.html; ii index.html
```

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

## Invoke-RestMethod 

To POST a file on the file system you can use:
```powershell
$jsonPayload = Get-Content(Get-Item -Path ./path/to/file.json) -Raw; `
Invoke-RestMethod -Uri http://example.com -Method POST -Body $jsonPayload
```

## Start-BitsTransfer - Background Intelligent Transfer (BITS)

`Start-BitsTransfer` is similar to `Invoke-WebRequest` but is much **MUCH** faster when dealing with larger files:
```powershell
Start-BitsTransfer -Source "https://example.com/path/to/largefile.iso" -Destination "C:\Downloads\largefile.iso"
```
