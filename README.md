# Cuckoo Sandbox

This Ansible role is used to setup [Cuckoo](https://cuckoosandbox.org) on an Ubuntu 18.04 box. To keep things simple, the following is assumed:  

- Single instance setup
- KVM as hypervisor (XenServer coming soon)
- PostgreSQL as database is already installed and configured. You can use my very basic Postgres role to install the database: https://github.com/drbeni/ansible-role-postgres
- The Ubuntu 18.04 is already hardened. A good Ansible role for this task is https://github.com/githubixx/ansible-role-harden-linux
- The host firewall is configured so that the VMs can reach the Cuckoo service on TCP port 2042


## Role Variables

Host variables with NO default values:
```
# Create password with python -c 'import crypt; print crypt.crypt("This is my Password", "$1$SomeSalt$")'

cuckoo_user_password: <password hash, see comment above>
cuckoo_public_keys:
  - /path/to/your/ssh/public/keys

cuckoo_server_ip: <IP of the sandbox server as the VMs see it>

db_user: <username of the DB user>
db_password: <password of db_user>
db_name: <database name>

# Example values. VMs must be created manually/outside of this role
cuckoo_sandbox_vms:
  - name: "win7x64_02"
    ip: "192.168.122.12"
    platform: windows
    snapshot: snap5
    interface: virbr0
    tags: win7x64
    osprofile: Win7SP1x64
  - name: "win7x64_01"
    ip: "192.168.122.11"
    platform: windows
    snapshot: snap1
    interface: virbr0
    tags: win7x64
    osprofile: Win7SP1x64  
```

Variables with default values:
```
cuckoo_user_uid: 1234
cuckoo_user_shell: "/bin/bash"

cuckoo_user: cuckoo
cuckoo_user_home: /home/cuckoo
cuckoo_virtualenv: /home/cuckoo/venv

kvm_interface_name: virbr0
```

## Example Playbook  

```
-
  hosts: sandboxes
  roles:   
    -
      role: ansible-role-cuckoo
      tags: role-cuckoo
```

## How to use it

1. Configure the role variables except cuckoo_sandbox_vm
2. Run the role
3. Create at least one sandbox VM, snapshot it when ready
4. Configure at least one cuckoo_sandbox_vm in your vars
5. Run the role again
6. Login to the cuckoo server, change to cuckoo user, activate virtualenv
7. Start cuckoo with ``` supervisord -c .cuckoo/supervisord.conf ```


## License

GNU GENERAL PUBLIC LICENSE Version 3
