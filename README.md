# Ansible Role: sshd

An Ansible Role to configure an OpenSSH server.

[![Actions Status](https://github.com/tristan-weil/ansible-role-sshd/workflows/molecule/badge.svg?branch=master)](https://github.com/tristan-weil/ansible-role-sshd/actions)

## Role Variables

Available variables are listed below, (see also `defaults/main.yml`).

Mandatory variables:

| Variable      | Description |
| :------------ | :---------- |

Optional variables:

| Variable      | Default | Description |
| :------------ | :------ | :---------- |
| sshd_config   | {}      | a dictionary of allowed parameters (see http://man.openbsd.org/sshd_config) that will be merged with the var *sshd_default_config*
    
The default configuration of the OpenSSH server is available in `sshd_default_config` in `vars/main.yml`.
It is possible to override or add new options using the `sshd_config` dictionary.
The variables in the dictionary must respect the name and the grammar of the real `sshd_config` file.

There is one special case: the `Match` key. 
Because the grammar is not easily translated into `jinja2`, let's modify it in the dictionary.
The key stay `Match` but there are 2 children keys: `Conditions` and `Parameters`.
`Conditions` are all keys and parameters found on the same line as `Match` in a `sshd_config` file.
`Parameters` are the keys and parameters found in a `Match` block.

## Example Playbook

    - hosts: 'webservers'
    
      roles:
        - role: 'ansible-role-sshd'
          sshd_config:
            AllowGroups: ssh-access
            AllowTcpForwarding: 'yes'
            
            Match:
              Conditions:
                User: root
                LocalAddress: 192.168.4.1
              Parameters:
                PermitRootLogin: prohibit-password
                AllowGroups: root

## Todo

None.

## Dependencies

See [requirements_galaxy.yml](https://github.com/tristan-weil/ansible-role-sshd/blob/master/requirements_galaxy.yml)

## Supported platforms

See [meta/main.yml](https://github.com/tristan-weil/ansible-role-sshd/blob/master/meta/main.yml)

## License

See [LICENSE.md](https://github.com/tristan-weil/ansible-role-sshd/blob/master/LICENSE.md)
