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