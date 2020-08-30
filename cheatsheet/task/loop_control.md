# loop_control:

## Limiting loop output with `label`

since: 2.1

The `loop_control` keyword lets you manage your loops in useful ways.
Limiting loop output with label

since: 2.2

When looping over complex data structures, the console output of your task can be enormous. To limit the displayed output, use the label directive with `loop_control`:

```yaml
- name: create servers
  digital_ocean:
    name: "{{ item.name }}"
    state: present
  loop:
    - name: server1
      disks: 3gb
      ram: 15Gb
      network:
        nic01: 100Gb
        nic02: 10Gb
        ...
  loop_control:
    label: "{{ item.name }}"
```

The output of this task will display just the name field for each item instead of the entire contents of the multi-line `{{ item }}` variable.


> :warning: This is for making console output more readable, not protecting sensitive data. If there is sensitive data in loop, set `no_log: yes` on the task to prevent disclosure.


## Pausing within a loop

since: 2.2

To control the time (in seconds) between the execution of each item in a task loop, use the pause directive with `loop_control`:

```yaml
- name: create servers, pause 3s before creating next
  digital_ocean:
    name: "{{ item }}"
    state: present
  loop:
    - server1
    - server2
  loop_control:
    pause: 3
```

## Tracking progress through a loop with `index_var`

since: 2.5

To keep track of where you are in a loop, use the `index_var` directive with `loop_control`. This directive specifies a variable name to contain the current loop index:

```yaml
- name: count our fruit
  debug:
    msg: "{{ item }} with index {{ my_idx }}"
  loop:
    - apple
    - banana
    - pear
  loop_control:
    index_var: my_idx
```

## Defining inner and outer variable names with `loop_var`

since: 2.1

You can nest two looping tasks using `include_tasks`. However, by default Ansible sets the loop variable item for each loop. This means the inner, nested loop will overwrite the value of item from the outer loop. You can specify the name of the variable for each loop using `loop_var` with `loop_control`:

```yaml
# main.yml
- include_tasks: inner.yml
  loop:
    - 1
    - 2
    - 3
  loop_control:
    loop_var: outer_item
```

```yaml
# inner.yml
- debug:
    msg: "outer item={{ outer_item }} inner item={{ item }}"
  loop:
    - a
    - b
    - c
```

> :warning: If Ansible detects that the current loop is using a variable which has already been defined, it will raise an error to fail the task.

## Extended loop variables

since: 2.8.

As of Ansible 2.8 you can get extended loop information using the extended option to loop control. This option will expose the following information.
Variable | Description
--- | ---
`ansible_loop.allitems` | The list of all items in the loop
`ansible_loop.index` | The current iteration of the loop. (1 indexed)
`ansible_loop.index0` | The current iteration of the loop. (0 indexed)
`ansible_loop.revindex` | The number of iterations from the end of the loop (1 indexed)
`ansible_loop.revindex0` | The number of iterations from the end of the loop (0 indexed)
`ansible_loop.first` | True if first iteration
`ansible_loop.last` | True if last iteration
`ansible_loop.length` | The number of items in the loop
`ansible_loop.previtem` | The item from the previous iteration of the loop. Undefined during the first iteration.
`ansible_loop.nextitem` | The item from the following iteration of the loop. Undefined during the last iteration.

```yaml
loop_control:
  extended: yes
```


