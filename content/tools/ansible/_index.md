---
title: Ansible
tags:
- ansible
- automation
---

[Ansible](https://www.ansible.com/) provides open-source automation that reduces complexity and runs everywhere. 
<!--more-->
Using Ansible lets you automate virtually any task. Ansible is available as a [Community version](https://docs.ansible.com/ansible/latest/index.html) 
and a Red Hat licenced version.

> Unfortunately ansible requires a Linux machine,[Windows **cannot** run ansible natively](https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html#installing-ansible-on-windows)

## Handy flags

The table below shows some of the flags that can be added when running an Ansible playbook.

| Flag      | Description                                                     |
|-----------|-----------------------------------------------------------------|
| `--diff`  | Show the difference caused from the execution of the task       |
| `--check` | Run in ['Check Mode'](https://docs.ansible.com/ansible/2.8/user_guide/playbooks_checkmode.html) (i.e. don't make any changes to the system) |

## Check which plugins are installed

```shell
ansible-galaxy collection list
```

## Pages

{{% children sort="title" description="true" %}}