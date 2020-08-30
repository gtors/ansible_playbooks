# until:

since: 1.4

You can use the until keyword to retry a task until a certain condition is met. Here’s an example:

```yaml
- shell: /usr/bin/foo
  register: result
  until: result.stdout.find("all systems go") != -1
  retries: 5
  delay: 10
```

This task runs up to 5 times with a delay of 10 seconds between each attempt. If the result of any attempt has “all systems go” in its stdout, the task succeeds. The default value for “retries” is 3 and “delay” is 5.

To see the results of individual retries, run the play with -vv.

When you run a task with until and register the result as a variable, the registered variable will include a key called “attempts”, which records the number of the retries for the task.


> :warning: You must set the until parameter if you want a task to retry. If until is not defined, the value for the retries parameter is forced to 1.

