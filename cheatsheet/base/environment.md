# environment:

since: 1.1

The environment keyword allows you to set an environment varaible for the action to be taken on the remote target. For example, it is quite possible that you may need to set a proxy for a task that does http requests. Or maybe a utility or script that are called may also need certain environment variables set to run properly.

```yaml
- hosts: all
  remote_user: root

  tasks:

    - name: Install cobbler
      package:
        name: cobbler
        state: present
      environment:
        http_proxy: http://proxy.example.com:8080
```

> :warning: does not affect Ansible itself, ONLY the context of the specific task action and this does not include
>  Ansible’s own configuration settings nor the execution of any other plugins, including lookups, filters, and so on.

The environment can also be stored in a variable, and accessed like so:

```yaml
- hosts: all
  remote_user: root

  # here we make a variable named "proxy_env" that is a dictionary
  vars:
    proxy_env:
      http_proxy: http://proxy.example.com:8080

  tasks:

    - name: Install cobbler
      package:
        name: cobbler
        state: present
      environment: "{{ proxy_env }}"
```

You can also use it at a play level:

```yaml
- hosts: testhost

  roles:
     - php
     - nginx

  environment:
    http_proxy: http://proxy.example.com:8080
```

While just proxy settings were shown above, any number of settings can be supplied. The most logical place to define an environment hash might be a group_vars file, like so:

```yaml
---
# file: group_vars/boston

ntp_server: ntp.bos.example.com
backup: bak.bos.example.com
proxy_env:
  http_proxy: http://proxy.bos.example.com:8080
  https_proxy: http://proxy.bos.example.com:8080
```
