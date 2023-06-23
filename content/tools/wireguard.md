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

It helps to think of WireGuard primarily as a network interface, like any other. 
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

| Command                                         | Description                                               |
|-------------------------------------------------|-----------------------------------------------------------|
| `wg genkey > wg_private`                        | Generate new private key writing it to the file `private` |
| `wg pubkey < wg_private`                        | Generate the public key from a private key                |
| `ip addr`                                       | List the IP addresses                                     |
| `wg`                                            | Show the status of the wireguard interfaces               |
| `sudo wg set wg0 peer <PEER_PUBLIC_KEY> remove` | Remove the peer from the wireguard interface              |
| `sudo ip link delete wg0`                       | Delete the wg0 interface                                  |  
| `sudo wg-quick up wg0`                          | Apply the `/etc/wireguard/wg0.conf` config file           |
| `sudo wg-quick down wg0`                        | Removed the wg0 configuration                             |

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

## Configuration

By default, wireguard looks for configuration files in the `/etc/wireguard/` directory.
The `wg-quick up <config>` looks for a configuration file within the `/etc/wireguard/` directory.

For example; if you run the command `wg-quick up wg0`, wireguard will load the configuration from `/etc/wireguard/wg0.conf`

A simple configuration file can look like this:
```text
[Interface]
Address = 10.10.10.10/32
ListenPort = 51000 # or whatever port you want to run wg on
PrivateKey = <contents of this-machines-private.key>

[Peer]
# laptop
PublicKey = <contents of laptop-public.key>
AllowedIPs = 10.10.10.11/32 # any available IP in the VPN range
```

## Creating a Systemd service

If you would like to make sure wireguard runs all the time and starts after a system restart you can create a systemd service.

Create the service file `/etc/systemd/system/wireguard.service` with: `sudo nano /etc/systemd/system/wireguard.service`
and add the following text to the file:

```text
[Unit]
Description=Start wireguard.
After=network.target

[Service]
Type=simple
ExecStart=wg-quick up wg0
RemainAfterExit=true
ExecStop=wg-quick down wg0
StandardOutput=journal
StandardError=journal
User=root
Group=root

[Install]
WantedBy=multi-user.target
```
Then:
```shell
# Reload the systemctl daemon to detect the new file/service
$ sudo systemctl daemon-reload

# Start the service
$ sudo systemctl start wireguard

# And check its status
$ sudo systemctl status wireguard
● wireguard.service - Start wireguard.
   Loaded: loaded (/etc/systemd/system/wireguard.service; disabled; vendor preset: enabled)
   Active: active (exited) since Sat 2023-06-24 08:38:02 AEST; 2s ago
  Process: 19584 ExecStart=/usr/bin/wg-quick up wg0 (code=exited, status=0/SUCCESS)
 Main PID: 19584 (code=exited, status=0/SUCCESS)

Jun 24 08:38:02 server wg-quick[19584]: [#] wg setconf wg0 /dev/fd/63
Jun 24 08:38:02 server wg-quick[19584]: [#] ip -4 address add ....

# You can also check wireguard directly using
$ sudo wg

# To make the service run on system start
$ sudo systemctl enable wireguard

Created symlink /etc/systemd/system/multi-user.target.wants/wireguard.service → /etc/systemd/system/wireguard.service.
```

You can test that everything is working as expected by rebooting the system `sudo reboot now` and checking that 
the wireguard service and wireguard are working:
```shell
# Reboot
$ sudo reboot now

# After the system is back up...

# Check the status of the service
$ sudo systemctl status wireguard

# And check wireguard
$ sudo wg
```

