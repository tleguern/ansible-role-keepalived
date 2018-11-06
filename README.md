ansible-keepalived
==================

This role configures keepalived without arm twisting. Write the configuration the way you want.

Requirements
------------

This role doesn't install nor compile from source keepalived. It should be already present in some way (ie: provisionning tool like Packer).

Role Variables
--------------

* `keepalived_global_defs`: configure the `global_defs` block ;
* `keepalived_vrrp_scripts`: configure one or more `vrrp_script` ;
* `keepalived_vrrp_instances`: configure one or more `vrrp_instance`.

### keepalived_global_defs

* `router_id`: define the router_id of this keepalived instance ;
* `notification_email`: a YAML list of email addresses ;
* `notification_email_from`: the from: address ;
* `smtp_server`: remote SMTP server to use ;
* `no_email_faults`: do not send email, value should be true or false ;
* `default_interface`: set the default interface, the default value is eth0.

### keepalived_vrrp_scripts

This variable contains a list of vrrp_script values structured like this:

- `name`: name of the monitored service, mandatory ;
- `script`: shell excerpt allowing to monitor the service, mandatory ;
- `interval`: interval of time in seconds between to run of `script`, the default value is 2 ;
- `timeout`: seconds after which `script` is considered to have failed ;
- `weight`: reduce priority by `weight` on FAULT state, the default value is 2 ;
- `rise`: exit FAULT state if `script` returned zero `rise` time, the default value is 2 ;
- `fall`: enter FAULT state if `script` returned non-zero `fall` time, the default value is 2 ;
- `init_fail`: assume `script` initially is in failed state.

### keepalived_vrrp_instances

This variable contains a list of vrrp_instance values structured like this:

- `name`: instance name, mandatory ;
- `virtual_router_id`: specify which VRRP router id this instance belongs to, mandatory ;
- `advert_int`: advertisement interval in seconds, the default value is 1 ;
- `priority`: specify the priority of this instance in the VRRP router, mandatory ;
- `state`: specify the instance state at startup, either MASTER or BACKUP, mandatory ;
- `unicast_src_ip`: IP address this instance should listen on, optionnal ;
- `unicast_peer`: list of IP addresses allowed to communicate with this instance, optionnal ;
- `interface`: interface to use, mandatory ;
- `virtual_ipaddresses`: a list of IP addresses to share in the VRRP router ;
- `track_interfaces`: a list of interfaces to track ;
- `track_script`: a service which state is tracked, defined in `keepalived_vrrp_scripts` ;
- `notify_backup`: execute `notify_backup` when state transition to BACKUP, optionnal ;
- `notify_master`: execute `notify_backup` when state transition to MASTER , optionnal ;
- `notify_fault`: execute `notify_backup` when state transition to FAULT, optionnal ;
- `authentication`: set IPSSEC-AH authentication, default value is false.

Dependencies
------------

None.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```
    - hosts: wwwmaster
      vars:
      - keepalived_global_defs:
        router_id: 1
      - keepalived_vrrp_scripts:
        - name: haproxy
          script: killall -0 haproxy
      - keepalived_vrrp_instances:
        - name: HAPROXY
           virtual_router_id: 42
           priority: 100
           state: MASTER
           unicast_src_ip: "{{ hostvars[wwwmaster].ansible_default_ipv4.address }}"
           unicast_peers:
             - "{{ hostvars[wwwbackup].ansible_default_ipv4.address }}"
           interface: eth0
           virtual_ipaddresses:
             - "{{ vip_front }}"
           track_script: haproxy
      roles:
      - role: ansible-keepalived
```

License
-------

ISC

Author Information
------------------

This role was created in 2018 by Tristan Le Guern on the behalf of Deveryware.
