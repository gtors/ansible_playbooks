# delegate_facts:

By default, any fact gathered by a delegated task are assigned to the inventory_hostname (the current host) instead of the host which actually produced the facts (the delegated to host). The directive delegate_facts may be set to True to assign the task’s gathered facts to the delegated host instead of the current one.:

```yaml
---
- hosts: app_servers

  tasks:
    - name: gather facts from db servers
      setup:
      delegate_to: "{{item}}"
      delegate_facts: True
      loop: "{{groups['dbservers']}}"
...
```

The above will gather facts for the machines in the dbservers group and assign the facts to those machines and not to app_servers. This way you can lookup hostvars[‘dbhost1’][‘ansible_default_ipv4’][‘address’] even though dbservers were not part of the play, or left out by using –limit.

