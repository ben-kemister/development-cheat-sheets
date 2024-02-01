---
title: "Playbooks"
date: 2024-02-02T07:39:05+11:00
tags:
- ansible
- tasks
- playbook
---

[Ansible Playbooks](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_intro.html) offer a repeatable, reusable, 
simple configuration management and multi-machine deployment system, one that is well suited to deploying complex applications. 
<!--more-->
If you need to execute a [task](tasks) with Ansible more than once, write a playbook and put it under source control.


## Hosts

### Run on first host in group

Use `hosts: group_name[0]` to select the first host in a group. For example:

```yaml
- name: Retrieve K3S_TOKEN from server
  hosts: server[0]
  gather_facts: true
  become: true
  tasks:
    - name: Echo token
      command: /bin/cat /var/lib/rancher/k3s/server/node-token
      register: token
```