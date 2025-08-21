---
title: Pi-hole
tags:
 - linux
 - development
 - network
---

[Pi-hole](https://pi-hole.net/) is a Linux network-level advertisement and Internet tracker blocking application which acts as a DNS sinkhole and optionally a DHCP server, intended for use on a private network. 
It is designed for low-power embedded devices with network capability, such as the Raspberry Pi, but can be installed on almost any Linux machine.[
<!--more-->

## Admin API

> Note: The API (and security) have been changed in v6 for more information see: [Authentication | Pi-hole documentation](https://docs.pi-hole.net/api/auth/)

There is an [admin api](https://discourse.pi-hole.net/t/pi-hole-api/1863) that can be used by script or their-party app to control Pi-hole.

The admin api is available at: http://pi.hole/admin/api.php?<ADMIN_FUNTION>&auth=<TOKEN>

The TOKEN (WEBPASSWORD) can be found here: `/etc/pihole/setupVars.conf`, as per the advice in [this blog post](https://pi-hole.net/blog/2022/11/17/upcoming-changes-authentication-for-more-api-endpoints-required/#page-content).

    Note 1: An incorrect, or missing, auth parameter in the request will result in a 200 response with an empty array [] 
    in the response payload.

    Note 2: It looks like the WEBPASSWORD changes when a pihole update (pihole -up) is performed

## Temporary Disable

Using the Admin API you can temporarily disable the ad blocking by using the command:
http://pi.hole/admin/api.php?disable=<SECONDS>&auth=<TOKEN>

To re-enable the ad blocking you can use:
http://pi.hole/admin/api.php?enable&auth=<TOKEN>

## Systemd

When installed on a linux machine pi-hole can be managed by `systemd` (using `systemctl`), for example:

```shell
# Start pihole
sudo systemctl start pihole-FTL

# Stop pihole
sudo systemctl stop pihole-FTL

# Disable
sudo systemctl disable pihole-FTL
```

## Handy links

* [pihole - Docker Image](https://hub.docker.com/r/pihole/pihole)
* [pihole Docker - github repo](https://github.com/pi-hole/docker-pi-hole#readme)
* [pihole - Helm Chart](https://artifacthub.io/packages/helm/mojo2600/pihole)
* [pihole-kubernetes - github repo](https://github.com/MoJo2600/pihole-kubernetes)
