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

## Concepts

It helps to think of WireGuard primarly as a network interface, like any other. 
It will have the usual attributes, like IP address, CIDR, and there will be some routing associated with it. 
But it also has WireGuard specific attributes, which handle the VPN part of things.

All of this can be configured via different tools. 
WireGuard itself ships its own tools in the userspace package wireguard-tools: [wg](https://manpages.ubuntu.com/manpages/jammy/man8/wg.8.html) 
and [wg-quick](https://manpages.ubuntu.com/manpages/jammy/man8/wg-quick.8.html).

Important attributes of a WireGuard interface are:

* _private key_: together with the corresponding public key, they are used to authenticate and encrypt data. This is generated with the wg genkey command.
* _listen port_: the UDP port that WireGuard will be listening to for incoming traffic.
* List of _peers_, each one with:
  * _public key_: the public counterpart of the private key. Generated from the private key of that peer, using the wg pubkey command.
  * _endpoint_: where to send the encrypted traffic to. This is optional, but at least one of the corresponding peers must have it to bootstrap the connection.
  * _allowed IPs_: list of inner tunnel destination networks or addresses for this peer when sending traffic, or, when receiving traffic, which source networks or addresses are allowed to send traffic to us.

## Tutorials

* [Ubuntu - Wireguard on an internal system](https://ubuntu.com/server/docs/wireguard-vpn-peer2site-inside)
* [Generating WireGuard QR codes](https://serversideup.net/generating-wireguard-qr-codes-for-fast-mobile-deployments/)

## Handy Commands

| Command                                       | Description                                               |
|-----------------------------------------------|-----------------------------------------------------------|
| `wg genkey > wg_private`                      | Generate new private key writing it to the file `private` |
| `wg pubkey < wg_private`                      | Generate the public key from a private key                |
| `ip addr`                                     | List the IP addresses                                     |
| `wg`                                          | Show the status of the wireguard interfaces               |
| `sudo wg set wg0 peer <PEER_PUBLIC_KEY> remove` | Remove the peer from the wireguard interface |
| `sudo ip link delete wg0`                     | Delete the wg0 interface |  
| `sudo wg-quick up wg0`                        | Applys the `/etc/wireguard/wg0.conf` config file |
| `sudo wg-quick down wg0` | Removed the wg0 configuration |

## Basic (peer to peer) connection

The steps below show how configure a very basic peer-to-peer connection manually using wireguard.

### Peer 1

* `wg genkey > wg_private`
* `wg pubkey < wg_private`
* `sudo ip link add wg0 type wireguard`
* `sudo ip addr add 10.0.0.1/24 dev wg0`
* `sudo wg set wg0 private-key ./wg_private`
* `sudo ip link set wg0 up`
* `sudo wg set wg0 peer <PEER_2_PUBLIC_KEY> allowed-ips 10.0.0.X/32 endpoint <PEER_2_IP or 0.0.0.0>:<PEER_2_PORT>`
* `ping 10.0.0.X` - ping peer 2

### Peer 2

* `wg genkey > wg_private`
* `wg pubkey < wg_private`
* `sudo ip link add wg0 type wireguard`
* `sudo ip addr add 10.0.0.X/24 dev wg0`
* `sudo wg set wg0 private-key ./wg_private`
* `sudo ip link set wg0 up`
* `sudo wg set wg0 peer <PEER_1_PUBLIC_KEY> allowed-ips 10.0.0.1/32 endpoint <PEER_1_ADDRESS>:<PEER_1_PORT> persistent-keepalive 25`
* `ping 10.0.0.1` - ping peer 1

## Generating QR codes for clients

* install qrencode package:
  `sudo apt install qrencode`
* Create keys for client (replace mobile with device name):
  ```shell
    sudo mkdir -p /etc/wireguard/clients; wg genkey | sudo tee /etc/wireguard/clients/mobile.key | wg pubkey | sudo tee /etc/wireguard/clients/mobile.key.pub
    ```
* Create the client config:
  ```shell
  sudo nano /etc/wireguard/clients/ben_s7_tablet.conf
  ```
  ```text
  [Interface]
  PrivateKey = <Contents of /etc/wireguard/clients/mobile.key>
  Address = <YOUR_VPN_PRIVATE_IP>/24
  DNS = <YOUR_NETWORK_DNS_ADDRESS>
  
  [Peer]
  PublicKey = <YOUR_SERVER_PUBLIC_KEY>
  AllowedIPs = <THE_IP_RANGE_OF_THE PRIVATE_NETWORK>/24
  Endpoint = <YOUR_SERVER_WAN_IP or PUBLIC DNS>:51820
  ```
* Add the IP address and public key (/etc/wireguard/clients/mobile.key.pub) to the peer configuration.
* Generate the QR code:
  ```shell
  # Switch to root user
  sudo -i
  # Generate the QR code
  qrencode -t ansiutf8 < /etc/wireguard/clients/mobile.conf
  ```
  
