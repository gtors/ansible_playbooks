# delegate_to:

If you want to perform a task on one host with reference to other hosts, use the ‘delegate_to’ keyword on a task. This is ideal for placing nodes in a load balanced pool, or removing them. It is also very useful for controlling outage windows. Be aware that it does not make sense to delegate all tasks, debug, add_host, include, etc always get executed on the controller. Using this with the ‘serial’ keyword to control the number of hosts executing at one time is also a good idea:

```yaml
---
- hosts: webservers
  serial: 5

  tasks:
    - name: take out of load balancer pool
      command: /usr/bin/take_out_of_pool {{ inventory_hostname }}
      delegate_to: 127.0.0.1

    - name: actual steps would go here
      yum:
        name: acme-web-stack
        state: latest

    - name: add back to load balancer pool
      command: /usr/bin/add_back_to_pool {{ inventory_hostname }}
      delegate_to: 127.0.0.1
...
```
