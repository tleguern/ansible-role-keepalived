ansible-keepalived
==================

This role configures keepalived without arm twisting. Write the configuration the way you want.

Requirements
------------

Another role or deployment method must be devised to use notification scripts such as `notify_backup`, `notify_fault` and `notify_master`.

Role Variables
--------------

* `keepalived_global_defs`: configure the `global_defs` block ;
* `keepalived_vrrp_scripts`: configure one or more `vrrp_script` ;
* `keepalived_vrrp_instances`: configure one or more `vrrp_instance`.

Dependencies
------------

The `keepalived` package should be installed first using a tool such as Packer.
However if it is not part of your toolchain the variable `keepalived_install` can be set to `True` to provoke an installation.

Example Playbook
----------------

```
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

License
-------

ISC

Author Information
------------------

This role was created in 2018 by Tristan Le Guern on the behalf of Deveryware.
