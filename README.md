Ansible beats role
=========

Installs Elastic's beats for forwarding logs.

Role Variables
--------------

 - `beats_config_*` - YAML representation of your beats config. This is templated directly into the configuration file as YAML. See the [example configuration](https://github.com/elastic/filebeat/blob/master/etc/filebeat.yml) for an exhaustive list of configuration options. Defaults to:

  ``` yaml
  beats_config_filebeat_filebeat:
    filebeat:
      prospectors:
        - paths:
            - /var/log/messages
            - /var/log/*.log
          input_type: log
  beats_config_filebeat_output:
    output:
      file:
        path: /tmp/filebeat
        filename: filebeat
  beats_config_filebeat_logging:
    logging:
      to_syslog: true
      level: error
  ```
 - `beats_ca_cert` - If provided, the contents of this variable will be placed into the file identified by `beats_ca_path` on the target host. You can then include the `beats_ca_path` within your configuration to properly authenticate your TLS connections to Logstash/Elasticsearch/etc.
 
  If you wish to load your CA certificate from a file, use the `file` lookup plugin, e.g.:
  ``` yaml
  beats_ca_cert: "{{ lookup('file', '/path/to/ca.crt') }}"
  ```
 - `beats_ca_path` - If a CA certificate is provided in `beats_ca_cert`, it will be created at this path. 

Common Configurations
---------------------

Connecting to Elasticsearch:

  ``` yaml
  beats_config_filebeat_filebeat:
    filebeat:
      prospectors:
        - paths:
            - /var/log/messages
            - /var/log/*.log
          input_type: log
  beats_config_filebeat_output:
    output:
      elasticsearch:
        hosts:
          - "http://localhost:9200"
        username: "bob"
        password: "12345"
  beats_config_filebeat_logging:
    logging:
      to_syslog: true
      level: error
  ```

License
-------

BSD

Author Information
------------------

David Wittman
Yann Autissier
