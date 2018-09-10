# Ansible Role: sshd

An Ansible Role to configure a OpenSSH server for Debian and OpenBSD.

In order to allow multiple other roles to configure extra options to the OpenSSH server (i.e. adding some Match conditions), 
the role allows to write the custom configs in differents files (the default file is `10_sshd_config`).
They are at the end assembled in the `/etc/ssh/sshd_config` file.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml` and `vars/main.yml`):

    sshd_config: {}                                 # a dict of allowed parameters (see http://man.openbsd.org/sshd_config).
                                                    # by default, its the content of the var 'sshd_default_config'
    
The default configuration of the OpenSSH server is available in `sshd_default_config` in `vars/main.yml`.
It is possible to override or add new options using the `sshd_config` dictionnary if the destination filename is not changed because both variables are merged.
The variables in the dictionnary must respect the name and the grammar of the real `sshd_config` file.

There is one special case: the `Match` key. 
Because the grammar is not easily translated into `jinja2`, let's modify it in the dictionnary.
The key stay `Match` but there are 2 children keys: `Conditions` and `Parameters`.
`Conditions` are all keys and parameters found on the same line as `Match` in a `sshd_config` file.
`Parameters` are the keys and parameters found in a `Match` block.

    
    sshd_config_filename_part: 10_sshd_config       # the name of the file containing the configuration
                                                        
This variable points to the file containing the current configuration found in the `ssh_config` variable.
It will be assembled with others files found in `/etc/ssh/sshd_config_assemble` directory to create the `/etc/ssh/sshd_config` file. 
The default file should remain 10_sshd_config and be installed early in your playbook.
Note that if a custom filename is used, the `sshd_default_config` values won't be merged with the current configuration found in the `ssh_config` variable.

    sshd_config_filename_part_state: present        # present|absent

This allows to remove some configuration files.
    
    sshd_firewall_config_ipv4_rules: []             # special ipv4 firewall rules (but you already should have opened a port) 
    sshd_firewall_config_ipv6_rules: []             # special ipv6 firewall rules (but you already should have opened a port)

Sometimes, extra firewall rules are needed to open custom ports.
These variables allows to create them.

## Dependencies

t18s.fr_firewall_config

## Example Playbook

    - hosts: webservers
    
      roles:
        - role: t18s.fr_sshd
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

          sshd_config_filename_part: 50_drbd

## Todo

None.

## License

```
Copyright (c) 2018 Tristan Weil <titou@lab.t18s.fr>

Permission to use, copy, modify, and distribute this software for any
purpose with or without fee is hereby granted, provided that the above
copyright notice and this permission notice appear in all copies.

THE SOFTWARE IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL WARRANTIES
WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF
MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR
ANY SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES
WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN
ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF
OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.
```
