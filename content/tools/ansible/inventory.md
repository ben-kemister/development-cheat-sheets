---
title: Inventory
tags:
- ansible
- automation
---

Inventories organize managed nodes in centralized files that provide Ansible with system information and network locations. 
<!--more-->
Using an inventory file, Ansible can manage a large number of hosts with a single command.

> To communicate with the hosts you must also ensure that your public SSH key is added to the `authorized_keys` file on each host.  
> Make sure you have:
> 1. Got a ssh key (`ls -la ~/.ssh/id_rsa`) or generated one with `ssh-keygen`.
> 2. Added it to the host with `ssh-copy-id <USER_ID_ON_TARGET>@<HOSTNAME>`

## Inventory file

The inventory file can be a `ini` or `yaml` file and can list the hosts in logical groups. 

### Connecting to the host with a different username

You can use the [behavioral inventory parameter](https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html#connecting-to-hosts-behavioral-inventory-parameters) 
`ansible_user` to connect to the host with a different username. 
For example:
```yaml
my_hosts:
  hosts:
    my_r_pi:
      ansible_user: pi
```
And then used with the command:
```shell
$ ansible my_hosts -m ping -i inventory.yaml
my_r_pi | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

