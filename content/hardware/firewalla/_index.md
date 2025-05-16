---
title: Firewalla
tags:
- harware
- newtwork
---

[Firewalla](https://firewalla.com/) is a networking device that acts as a smart firewall, providing cybersecurity, parental controls, 
and ad blocking for homes and small businesses. 
<!--more-->

## Status lights

### Gold SE

| Light	             | Firewalla Box Status                                   |
|--------------------|--------------------------------------------------------|
| Blinking Blue	     | Booting Up                                             | 
| Fast Blinking Blue | Underlying App Communication                           |
| Blinking Red       | Network Down                                           |
| Solid Red	         | System Error / Power On, Firewalla Software Not Active |
| Light Off	         | System Active, Internet Connected                      |


## Wireguard clients accessing local devices

By default, the wireguard network on Firewalla blocks locally devices from sending traffic to VPN clients.

If you need/want to allow VPN clients to access local resources you will need to add an `iptables` rule to allow local 
devices to send responses to your VPN clients.

SSH'ing into your Firewalla you will need to run:
```shell
iptables -I FR_FORWARD 1 -i <network_id> -o wg0 -j ACCEPT
```
For example, to allow traffic from the first ethernet port/network run: ``iptables -I FR_FORWARD 1 -i br0 -o wg0 -j ACCEPT``

I found this nugget of information on the Firewalla community site [here](https://help.firewalla.com/hc/en-us/community/posts/31993384761491-Unable-to-connect-to-local-network-from-WireGuard-VPN-connection).

### Make change persistent

To make this change persistent you will need to create an executable script in `/home/pi/.firewalla/config/post_main.d/`.

1. `cd /home/pi/.firewalla/config/post_main.d/`
2. `sudo vi iptables-wireguard.sh`
3. Paste:
    ```shell
    #!/bin/bash
    
    #
    # Description: This script updates the iptables to allow local traffic back to the wireguard network
    # See: https://help.firewalla.com/hc/en-us/community/posts/31993384761491-Unable-to-connect-to-local-network-from-WireGuard-VPN-connection
    #
    
    echo "Updating iptables to allow local traffic back to the wireguard network"
    
    sudo iptables -I FR_FORWARD 1 -i br1 -o wg0 -j ACCEPT
    
    echo "Completed with exit code: $?"
    ```
4. Save and Quit vi: `CTRL` then `:wq`
5. Make the script executable: `sudo chmod +x iptables-wireguard.sh`


## Sub Pages

{{% children sort="title" description="true" %}}

## Handy Links

* [How to access Firewalla using SSH?](https://help.firewalla.com/hc/en-us/articles/115004397274-How-to-access-Firewalla-using-SSH)
* [Customized Scripting](https://help.firewalla.com/hc/en-us/articles/360054056754-Customized-Scripting)