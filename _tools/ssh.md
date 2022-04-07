---
name: "ssh - Secure Shell Protocol"
tags:
  - ssh
  - bash
  - sh
  - powershell
  - remote
  - putty
---

# What is *ssh*
The Secure Shell Protocol (SSH) is a cryptographic network protocol for operating network services securely over an unsecured network. 
Its most notable applications are remote login and command-line execution
<!--more-->

# Generating ssh Keys

A ssh key can be generated using the command `ssh-keygen`

# Validating ssh key(s)

`ssh-keygen -y` will prompt you for the passphrase (if there is one).

    If you input the **correct** passphrase, it will show you the associated public key.
    If you input the **wrong** passphrase, it will display *load failed*.
    If the key has no passphrase, it will not prompt you for a passphrase and will immediately show you the associated public key.

[Information found here](https://stackoverflow.com/questions/4411457/how-do-i-verify-check-test-validate-my-ssh-passphrase).

# Paswordless ssh logins

Typically passwordless logins are based around having your public key listed in the `~/.ssh/authorized_keys` file on the host.

## Passwordless logins using PuTTY

You can configure Putty to use passwordless logins.

To setup Putty you need to:

* (optional) Generate a ssh key pair, if it doesn't exist.
* Save/convert your private ssh key into the *.ppk (PuTTY private key file format)
** Open the *PuTTY Key Generator* program
** Using the Conversion --> Import key function to open your private key
** Using the *Save private key* button, save the *.ppk file
** Copy the public key information from the *PuTTY Key Generator*'s top panel to the `~/.ssh/authorized_keys` file on the remote host.
* Update you PuTTY configuration
** The remote username to use: Connection --> Data --> Login Details
** The *ppk file generated from above: Connection --> SSH --> Auth --> Authentication parameters
** Save the configuration changes
* Test!

The steps above was mostly inspired from the information [here](https://www.host-telecom.com/guides/error-unable-to-use-key-file-when-using-putty/)

## Windows 10 OpenSSH Equivalent of `ssh-copy-id`

The script below will copy your public key to a remote linux host, this will allow for password less logins.

``` ps
type $env:USERPROFILE\.ssh\id_rsa.pub | ssh {IP-ADDRESS-OR-FQDN} "cat >> .ssh/authorized_keys"
```
See [this link](https://www.chrisjhart.com/Windows-10-ssh-copy-id/) for more information on this PowerShell command/script.
