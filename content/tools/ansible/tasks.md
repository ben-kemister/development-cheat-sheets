---
title: Tasks
tags:
- ansible
- tasks
---

This page contains information about common/handy ansible tasks.
<!--more-->

## Create a directory

```yaml
    - name: Make k3s configuration directory
      ansible.builtin.file:
        path: "/etc/rancher/k3s"
        mode: 0755
        state: directory
```

## Print contents of file

You can do this using [Ansible's debug module](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/debug_module.html), 
for example:

```yaml
    - name: Echo token
      command: /bin/cat /var/lib/rancher/k3s/server/node-token
      register: token

    - ansible.builtin.debug:
        msg:
        - "K3S_TOKEN: {{ token.stdout }}"
```

## Using a template

> Note the template (*.j2) file must be in a `template` directory adjacent to the playbook.

```yaml
    - name: Copy registries.yaml file
      when: private_registry is defined
      ansible.builtin.template:
        src: "registries.j2"
        dest: "/etc/rancher/k3s/registries.yaml"
        owner: root
        group: root
        mode: 0644
```

## When tests

You can use [string tests](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_tests.html#testing-strings) 
in your `when` statement to preform tasks under certain conditions. For example:

### hostname in array

```yaml
- hosts: k3s_nodes
  vars:
    k3s_registration_address: loadbalancer  # Typically a load balancer.
  pre_tasks:
    - name: Set each node to be a control node
      ansible.builtin.set_fact:
        k3s_control_node: true
      when: inventory_hostname in ['node2', 'node3']
  roles:
    - role: xanmanning.k3s
```

### hostname matches string

```yaml
- name: Build a cluster with HA control plane
  hosts: k3s_cluster
  pre_tasks:
    - name: Set each server host to be a control node
      ansible.builtin.set_fact:
        k3s_control_node: true
      when: inventory_hostname is match ("server.*")
  roles:
    - role: xanmanning.k3s
```