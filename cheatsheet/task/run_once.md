# run_once:

In some cases there may be a need to only run a task one time for a batch of hosts. This can be achieved by configuring “run_once” on a task:

```yaml
- command: /opt/application/upgrade_db.py
  run_once: true
```


This directive forces the task to attempt execution on the first host in the current batch and then applies all results and facts to all the hosts in the same batch.

This approach is similar to applying a conditional to a task such as:

```yaml
- command: /opt/application/upgrade_db.py
  when: inventory_hostname == webservers[0]
```

But the results are applied to all the hosts.

Like most tasks, this can be optionally paired with “delegate_to” to specify an individual host to execute on:

```yaml
- command: /opt/application/upgrade_db.py
  run_once: true
  delegate_to: web01.example.org
```

As always with delegation, the action will be executed on the delegated host, but the information is still that of the original host in the task.


> :warning: When used together with “serial”, tasks marked as “run_once” will be run on one host in each serial batch. If it’s crucial that the task is run only once regardless of “serial” mode, use when: inventory_hostname == ansible_play_hosts_all[0] construct.

> :warning: Any conditional (i.e when:) will use the variables of the ‘first host’ to decide if the task runs or not, no other hosts will be tested.

> :warning: If you want to avoid the default behaviour of setting the fact for all hosts, set delegate_facts: True for the specific task or block.
