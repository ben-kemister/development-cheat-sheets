---
title: Templates
tags:
- ansible
- tasks
- template
---

This page contains information about ansible [templates](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_templating.html).
<!--more-->
Ansible uses **Jinja2** templating to enable dynamic expressions and access to variables and facts.

## Template location

To be automatically detected template (*.j2) files must be in a `templates` directory adjacent to the playbook.


## Using a template

Examples:
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

```yaml
    - name: Set AdmissionConfiguration (PodSecurity) on control nodes
      ansible.builtin.template:
        src: psa.yaml.j2
        dest: "{{ k3s_server_dir }}/psa.yaml"
        mode: 0644
      become: "{{ k3s_become }}"
      when:
        - k3s_control_node is defined
        - k3s_control_node
```

### Looping through a directory of templates

```yaml
    - name: Ensure server files are copied to control nodes
      ansible.builtin.template:
        src: "{{ item }}"
        dest: "{{ k3s_server_dir }}/{{ item | basename | replace('.j2', '') }}"
        mode: 0644
      loop: "{{ k3s_server_files }}"
      become: "{{ k3s_become }}"
      when:
        - k3s_control_node is defined
        - k3s_control_node
        - k3s_server_files | length > 0
```

## Function within a template

### Loop through a list of values

```yaml
    {% if pod_security_configuration_exemptions_namespaces is defined %}
      # Additional namespace exceptions
    {% for namespace in pod_security_configuration_exemptions_namespaces %}
      - {{ namespace }}
    {% endfor %}
    {% endif %}
```