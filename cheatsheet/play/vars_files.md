# vars_files:

You can define variables in reusable variables files and/or in reusable roles. When you define variables in reusable variable files, the sensitive variables are separated from playbooks. This separation enables you to store your playbooks in a source control software and even share the playbooks, without the risk of exposing passwords or other sensitive and personal data.

```yaml
- hosts: all
  vars_files:
    - /vars/external_vars.yml

  tasks:

  - name: this is just a placeholder
    command: /bin/echo foo
```

The contents of each variables file is a simple YAML dictionary. For example:

```yaml
# in the above example, this would be vars/external_vars.yml
somevar: somevalue
password: magic
```
