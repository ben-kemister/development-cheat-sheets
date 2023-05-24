---
title: PowerShell Utilities
tags:
- scripting
- windows
- powershell
- terminal
- utilities
---

This page provides examples about the use of PowerShell **utilities**.
<!--more-->

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