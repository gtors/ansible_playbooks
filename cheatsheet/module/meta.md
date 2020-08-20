# meta:

## flush_handlers 

makes Ansible run any handler tasks which have thus far been notified. Ansible inserts these tasks internally at certain points to implicitly trigger handler runs (after pre/post tasks, the final role execution, and the main tasks section of your plays).

## refresh_inventory 
since: 2.0
forces the reload of the inventory, which in the case of dynamic inventory scripts means they will be re-executed. If the dynamic inventory script is using a cache, Ansible cannot know this and has no way of refreshing it (you can disable the cache or, if available for your specific inventory datasource (e.g. aws), you can use the an inventory plugin instead of an inventory script). This is mainly useful when additional hosts are created and users wish to use them instead of using the add_host module.

## noop 
since: 2.0
This literally does 'nothing'. It is mainly used internally and not recommended for general use.

## clear_facts 
since: 2.1
Causes the gathered facts for the hosts specified in the play's list of hosts to be cleared, including the fact cache.

## clear_host_errors 
since: 2.1
Clears the failed state (if any) from hosts specified in the play's list of hosts.

## end_play 
since: 2.2 
Causes the play to end without failing the host(s). Note that this affects all hosts.

## reset_connection 
since: 2.3 
Interrupts a persistent connection (i.e. ssh + control persist)

## end_host 
since: 2.8
Is a per-host variation of end_play. Causes the play to end for the current host without failing it.

> :warning: 
> * meta is not really a module nor `action_plugin` as such it cannot be overwritten.
> * `clear_facts` will remove the persistent facts from `set_fact` using `cacheable=True`, but not the current host variable it creates for the current run.
> * Looping on meta tasks is not supported.
> * This module is also supported for Windows targets.

```yaml
# Example showing flushing handlers on demand, not at end of play
- template:
    src: new.j2
    dest: /etc/config.txt
  notify: myhandler

- name: Force all notified handlers to run at this point, not waiting for normal sync points
  meta: flush_handlers

# Example showing how to refresh inventory during play
- name: Reload inventory, useful with dynamic inventories when play makes changes to the existing hosts
  cloud_guest:            # this is fake module
    name: newhost
    state: present

- name: Refresh inventory to ensure new instances exist in inventory
  meta: refresh_inventory

# Example showing how to clear all existing facts of targetted hosts
- name: Clear gathered facts from all currently targeted hosts
  meta: clear_facts

# Example showing how to continue using a failed target
- name: Bring host back to play after failure
  copy:
    src: file
    dest: /etc/file
  remote_user: imightnothavepermission

- meta: clear_host_errors

# Example showing how to reset an existing connection
- user:
    name: '{{ ansible_user }}'
    groups: input

- name: Reset ssh connection to allow user changes to affect 'current login user'
  meta: reset_connection

# Example showing how to end the play for specific targets
- name: End the play for hosts that run CentOS 6
  meta: end_host
  when:
  - ansible_distribution == 'CentOS'
  - ansible_distribution_major_version == '6'
```
