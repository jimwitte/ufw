ufw
===
Installs and configures ufw

Requirements
------------
Tested on Ubuntu 16.04

Role Variables
--------------
The following variable are defined in defaults/main.yml:

```
ufw_enable: "yes"
ufw_incoming_policy: "deny"
ufw_ipv6: "no"
ufw_logging: "off"
ufw_outgoing_policy: "allow"
ufw_routed_policy: "deny"
```

ufw Roles
---------
A `ufw_role` is a bundle (list) of rule that logically go together. 
The following role-based rules are defined in defaults/main.yml:

  * ufw_role.ssh
  * ufw_role.web
  * ufw_role.mongo
  * ufw_role.vault

You can add or modify these roles in defaults/main.yml. When defining a `ufw_role`, you may specify:

  * to_port (default=omit)
  * src (default=any)
  * proto (default=tcp)
  * rule (default=allow)

For example, you might define `ufw_role.jumphosts` that allows ssh access only your jumphosts:
```
ufw_role:
  jumphosts:
    - to_port: 22
      src: "192.168.100.100"
    - to_port: 22
      src: "192.168.100.101"
```

You might define a `web` role to open ports 80 and 443 to all hosts like this:
```
ufw_role:
  web:
    - to_port: 80
    - to_port: 443
```

default_ufw_rules variable
--------------------------
Set default ufw rules in defaults/main.yml. These are intended to be applied whenever the ufw role is applied. 
You can use a combination of pre-defined ufw roles or arbitrary rules. For example:

```
default_ufw_rules:
  - "{{ ufw_role.jumphosts }}"
  - rule: allow
    src: 192.168.100.100
    proto: tcp
    to_port: 22
```

ufw_rules variable
------------------
`ufw_rules` is intended to be specified in the playbook. The format for this variable is just like `default_ufw_rules`.


Playbook Examples
----------------
Apply `ufw_default_rules` and apply `web` ufw role:
```
- hosts: all
  roles:
    - {role: ufw, ufw_rules: "{{ [ufw_role.web] }}"}
```

Apply `ufw_default_rules` and apply arbitrary rules defined in playbook
```
- hosts: all
  vars:
    ufw_rules:
      - rule: "allow"
        proto: "tcp"
        src: "any"
        to_port: 22
      - rule: "allow"
        proto: "tcp"
        src: "any"
        to_port: 443
  roles:
    - ufw
```

License
-------
BSD

Author Information
------------------
Jim Witte
jim@thunderingbison.com