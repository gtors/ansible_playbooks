# ignore_errors:

Generally playbooks will stop executing any more steps on a host that has a task fail. Sometimes, though, you want to continue on. To do so, write a task that looks like this:

```yaml
- name: this will not be counted as a failure
  command: /bin/false
  ignore_errors: yes
```

Note that the above system only governs the return value of failure of the particular task, so if you have an undefined variable used or a syntax error, it will still raise an error that users will need to address. Note that this will not prevent failures on connection or execution issues. This feature only works when the task must be able to run and return a value of ‘failed’.

