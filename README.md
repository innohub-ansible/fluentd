Role Name
=========

This role installs fluentd (td-agent version) and sets sources and matches in
its config file.

Requirements
------------

Make sure that your directories are writable by td-agent user.

Role Variables
--------------

fluentd_plugins : defines plugins to install : defaults to []

ex.
```
fluentd_plugins:
  - fluent-plugin-record-reformer
  - fluent-plugin-record-modifier
```

fluentd_sources : defines fluentd sources : defaults to []

ex.
```
fluentd_sources:
  - params:
      type: "syslog"
      port: "5140"
      bind: "0.0.0.0"
      tag: "syslog.log"
```

fluentd_matches : defines fluentd matches : defaults to []

ex.
```
fluentd_matches:
  - match: "syslog.log"
    params:
      type: "file"
      path: "/var/log/td-agent/syslog.log"
      append: "true"
      include_tag_key: "true"
```

Dependencies
------------

None.

Example Playbook
----------------
```
- hosts: log_host_servers
  vars:
    fluentd_plugins:
      - fluent-plugin-record-reformer
      - fluent-plugin-record-modifier
    fluentd_sources:
      - params:
          type: "forward"
      - params:
          type: "syslog"
          port: "5140"
          bind: "0.0.0.0"
          tag: "syslog.log"
    fluentd_matches:
      - match: "syslog.log"
        params:
          type: "file"
          path: "/var/log/td-agent/syslog.log"
          append: 'true'
          include_tag_key: "true"
  tasks:
    - include: ../tasks/prerequisites.yml
    - include: ../tasks/fluentd.yml
  handlers:
    - include: ../handlers/main.yml

- hosts: log_client_servers
  vars:
    fluentd_plugins:
      - fluent-plugin-record-modifier
    fluentd_sources:
      - params:
          type: "syslog"
          port: "5140"
          bind: "0.0.0.0"
          tag: "syslog.log"
    fluentd_matches:
      - match: "syslog.log"
        params:
          type: "forward"
          flush_interval: "60s"
        server_params:
          host: "<ip of your log_host_server here>"
  tasks:
    - include: ../tasks/fluentd.yml
  handlers:
    - include: ../handlers/main.yml
```

License
-------

MIT

Author Information
------------------

Linus Ramos
