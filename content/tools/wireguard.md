---
title: Wireguard (VPN)
tags:
- wireguard
- vpn
- linux
- android
- windows
---

[WireGuard®](https://www.wireguard.com/) is an extremely simple yet fast and modern VPN that utilizes state-of-the-art cryptography. 
<!--more-->
It aims to be faster, simpler, leaner, and more useful than IPsec, while avoiding the massive headache. 
It intends to be considerably more performant than OpenVPN.

## Handy Commands

| Command                  | Description                                               |
|--------------------------|-----------------------------------------------------------|
| `wg genkey > wg_private` | Generate new private key writing it to the file `private` |
| `wg pubkey < wg_private` | Generate the public key from a private key                |
| `ip addr`                | List the IP addresses                                     |
| `wg`                     | Show the status of the wireguard interfaces               | 

## Server side

* `wg genkey > wg_private`
* `wg pubkey < wg_private`
* `sudo ip link add wg0 type wireguard`
* `sudo ip addr add 10.0.0.1/24 dev wg0`
* `sudo wg set wg0 private-key ./wg_private`
* `sudo ip link set wg0 up`
* `sudo wg set wg0 peer <PEERS_PUBLIC_KEY> allowed-ips 10.0.0.2/32`
