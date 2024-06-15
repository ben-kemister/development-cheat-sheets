---
title: Tasks
tags:
- ansible
- tasks
---

This page contains information about common/handy ansible [tasks](https://docs.ansible.com/ansible/latest/getting_started/basic_concepts.html#tasks).
<!--more-->
The definition of an ‘action’ to be applied to the managed host.

## Create a directory

```yaml
    - name: Make k3s configuration directory
      ansible.builtin.file:
        path: "/etc/rancher/k3s"
        mode: 0755
        state: directory
```

## Copy (multi-line) string to file

```yaml
- name: Copy mytext to a file
  vars:
    mytext: |
      Hello
      World
  ansible.builtin.copy:
    content: "{{ mytext }}"
    dest: /tmp/some_file.txt
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

## Variable defaults

You can use Jinja's default:

```yaml
- name: Create user
  user:
    name: "{{ my_variable | default('default_value') }}"
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