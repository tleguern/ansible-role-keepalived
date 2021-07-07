# ansible-keepalived

[![builds.sr.ht status](https://builds.sr.ht/~tleguern/ansible-role-keepalived.svg)](https://builds.sr.ht/~tleguern/ansible-role-keepalived?)

This role configures keepalived without arm twisting. Write the configuration the way you want.

Automatic testing is provided using molecule's delegated driver and <https://builds.sr.ht>.

## Requirements

Another role or deployment method must be devised to use notification scripts such as `notify_backup`, `notify_fault` and `notify_master`.

## Role Variables

* `keepalived_global_defs`: configure the `global_defs` block ;
* `keepalived_vrrp_scripts`: configure one or more `vrrp_script` ;
* `keepalived_vrrp_instances`: configure one or more `vrrp_instance`.
* `keepalived_flags`: flags to pass to the keepalived daemon (set `--log-detail --log-facility=7` by default)

## Default variables

* `keepalived_install`: Install keepalived package (`no` by default)
* `keepalived_packages`: list of packages to install keeplived (depending on distribution version)
* `keepalived_auto_restart`: automatically restart keepalived when conf is changed (`yes` by default)

## Dependencies

The `keepalived` package should be installed first using a tool such as Packer.
However if it is not part of your toolchain the variable `keepalived_install` can be set to `True` to provoke an installation.

## Example Playbook

```yaml
- hosts: wwwmaster
  vars:
    - keepalived_global_defs: |
        router_id 1
    - keepalived_vrrp_scripts:
        - name: haproxy
          content: |
            script "killall -0 haproxy"
    - keepalived_vrrp_instances:
        - name: HAPROXY
          content: |
            virtual_router_id 42
            state MASTER
            unicast_src_ip "{{ master_ip }}"
            unicast_peer {
              "{{ backup_ip }}"
            }
            interface eth0
            virtual_ipaddress {
              "{{ vip_front }}"
            }
            track_script {
              haproxy
            }
  roles:
    - role: ansible-keepalived
```

## License

ISC

## Contributing

Either send [send GitHub pull requests](https://github.com/tleguern/ansible-role-keepalived) or [send patches on SourceHut](https://lists.sr.ht/~tleguern/misc).

## Author Information

This role was created in 2018 by Tristan Le Guern on the behalf of Deveryware.
