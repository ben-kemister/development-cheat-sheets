---
title: ExternalDNS
tags:
- kubernetes
- networking
- dns
---

[ExternalDNS](https://kubernetes-sigs.github.io/external-dns/latest/) makes Kubernetes resources discoverable via public DNS servers. 
<!--more-->
Like KubeDNS, it retrieves a list of resources (Services, Ingresses, etc.) from the Kubernetes API to determine a desired list of DNS records. 
Unlike KubeDNS, however, **it is not a DNS server** itself, but merely configures other DNS providers accordingly (like [Pi-Hole](../pi-hole)).

## Links & References

* [ExternalDNS](https://kubernetes-sigs.github.io/external-dns/latest/)
* [ExternalDNS - Github](https://github.com/kubernetes-sigs/external-dns?tab=readme-ov-file)
* [Setting up ExternalDNS for Pi-hole](https://kubernetes-sigs.github.io/external-dns/v0.14.0/tutorials/pihole/)


