# local_action:

These commands will run on 127.0.0.1, which is the machine running Ansible. There is also a shorthand syntax that you can use on a per-task basis: ‘local_action’. It's a shorthand syntax for delegating to 127.0.0.1:

```yaml
  tasks:
    - name: recursively copy files from management server to target
      local_action: command rsync -a /path/to/files {{ inventory_hostname }}:/path/to/target/
```

Note that you must have passphrase-less SSH keys or an ssh-agent configured for this to work, otherwise rsync will need to ask for a passphrase.

In case you have to specify more arguments you can use the following syntax:

```yaml
  tasks:
    - name: Send summary mail
      local_action:
        module: mail
        subject: "Summary Mail"
        to: "{{ mail_recipient }}"
        body: "{{ mail_body }}"
      run_once: True
```
