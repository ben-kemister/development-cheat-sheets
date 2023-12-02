---
title: Vault
tags:
- ansible
- password
- encrypt
---

[Ansible Vault](https://docs.ansible.com/ansible/latest/vault_guide/vault.html) encrypts variables and files so you can 
protect sensitive content such as passwords or keys rather than leaving it visible as plaintext in playbooks or roles.
<!--more-->

> The examples below use a single password (and vault) which is requested when the command is executed. 
> This is fine for simple use-cases.
> There are other was to deal with vaults and passwords which are explained 
> [here](https://docs.ansible.com/ansible/latest/vault_guide/vault_managing_passwords.html#choosing-between-a-single-password-and-multiple-passwords).

## Encrypting a variable

```shell
echo -n 'password' | ansible-vault encrypt_string --ask-vault-pass --stdin-name 'vault_db_user_password'
```

## Using an encrypted variable

```yaml
...
  ansible_become_password: "{{ vault_db_user_password }}"
```

## View encrypted variable (from a file)

Syntax: `ansible localhost -m ansible.builtin.debug -a var="<VARIABLE_NAME>" -e "@<PATH_TO_FILE>" --ask-vault-pass`

```shell
ansible localhost -m ansible.builtin.debug -a var="vault_db_user_password" -e "@group_vars/all/vault.yaml" --ask-vault-pass
```

## Running a playbook with encrypted variables

```shell
ansible-playbook --ask-vault-pass -i inventory.yaml my-first-playbook.yaml
```


