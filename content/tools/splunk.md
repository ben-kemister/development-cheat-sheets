---
title: splunk
tags:
 - splunk
---

[Splunk](https://www.splunk.com/) is an advanced and scalable form of software that indexes and searches for log files 
within a system and analyzes data for operational intelligence.
<!--more-->

## HTTP Event Collector (HEC)

The HTTP Event Collector (HEC) is a passive REST endpoint on a Splunk Heavy Forwarder instance.
HEC enables you to send data over HTTP (or HTTPS) directly to Splunk Enterprise or Splunk Cloud Platform from your application.

### HEC Health endpoint

You can check if the Splunk HEC endpoint is healthy by sending a GET request to `http(s)://<SLUNK_HEAVY_FORWARDER>:8088/services/collector/health`.

If the service is healthy you will get a response body which looks something like:
```json
{
  "text": "HEC is healthy",
  "code":"17"
}
```

