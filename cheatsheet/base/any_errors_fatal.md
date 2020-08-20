# any_errors_fatal:

With the `any_errors_fatal` option, any failure on any host in a multi-host play will be treated as fatal and Ansible will exit as soon as all hosts in the current batch have finished the fatal task. Subsequent tasks and plays will not be executed. You can recover from what would be a fatal error by adding a rescue section to the block.

`any_errors_fatal` can be set at the play or block level:

```yaml
- hosts: somehosts
  any_errors_fatal: true
  roles:
    - myrole

- hosts: somehosts
  tasks:
    - block:
        - include_tasks: mytasks.yml
      any_errors_fatal: true
```

for finer-grained control `max_fail_percentage` can be used to abort the run after a given percentage of hosts has failed.

## Use case:

Sometimes `serial` execution is unsuitable; the number of hosts is unpredictable (because of dynamic inventory) and speed is crucial (simultaneous execution is required), but all tasks must be 100% successful to continue playbook execution.

For example, consider a service located in many datacenters with some load balancers to pass traffic from users to the service. There is a deploy playbook to upgrade service deb-packages. The playbook has the stages:

    disable traffic on load balancers (must be turned off simultaneously)
    gracefully stop the service
    upgrade software (this step includes tests and starting the service)
    enable traffic on the load balancers (which should be turned on simultaneously)

The service can’t be stopped with “alive” load balancers; they must be disabled first. Because of this, the second stage can’t be played if any server failed in the first stage.

For datacenter “A”, the playbook can be written this way:

```yaml
---
- hosts: load_balancers_dc_a
  any_errors_fatal: True

  tasks:
    - name: 'shutting down datacenter [ A ]'
      command: /usr/bin/disable-dc

- hosts: frontends_dc_a

  tasks:
    - name: 'stopping service'
      command: /usr/bin/stop-software
    - name: 'updating software'
      command: /usr/bin/upgrade-software

- hosts: load_balancers_dc_a

  tasks:
    - name: 'Starting datacenter [ A ]'
      command: /usr/bin/enable-dc
...
```

In this example Ansible will start the software upgrade on the front ends only if all of the load balancers are successfully disabled.
