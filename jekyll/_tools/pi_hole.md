---
tool: pi_hole
name: Pi-hole
tags:
 - linux
 - development
---

[Pi-hole](https://pi-hole.net/) is a Linux network-level advertisement and Internet tracker blocking application which acts as a DNS sinkhole and optionally a DHCP server, intended for use on a private network. 
It is designed for low-power embedded devices with network capability, such as the Raspberry Pi, but can be installed on almost any Linux machine.[
<!--more-->

## Admin API

There is an [admin api](https://discourse.pi-hole.net/t/pi-hole-api/1863) that can be used by script or their-party app to control Pi-hole.

The admin api is available at: http://pi.hole/admin/api.php?<ADMIN_FUNTION>auth=<TOKEN>

The TOKEN (WEBPASSWORD) can be found here: `/etc/pihole/setupVars.conf`, as per the advice in [this blog post](https://pi-hole.net/blog/2022/11/17/upcoming-changes-authentication-for-more-api-endpoints-required/#page-content).

    Note 1: An incorrect, or missing, auth parameter in the request will result in a 200 response with an empty array [] 
    in the response payload.

    Note 2: It looks like the WEBPASSWORD changes when a pihole update (pihole -up) is performed
