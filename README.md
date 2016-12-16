# Ansible beats role

[![Ansible Galaxy](https://img.shields.io/badge/galaxy-aya.beats-blue.svg?style=flat)](https://galaxy.ansible.com/aya/beats/)

An Ansible role to install Elastic's beats.

## Requirements

This Ansible role requires at least Ansible version 2.1.

## Role Variables

* `beats` - A list of beats to manage. All actions taken by this role will be applied to the following beats.
```
# The list of the packages managed by this role
beats:
  - filebeat
  - topbeat
  - packetbeat
  - metricbeat
```

* `beats_package` - A list of beats packages to install. The following package will be installed (or removed) in the requested version if available.
```
# The installation state of the beats packages, passed to the appropriate
# packaging module (yum/apt) as the 'state'.
# Set to 'present' to install, 'latest' to upgrade and 'absent' to remove.
beats_package:
  - { 'name': 'filebeat', 'state': 'present', 'version': '1.2.3' }
  - { 'name': 'topbeat', 'state': 'present', 'version': '1.2.3' }
  - { 'name': 'packetbeat', 'state': 'present', 'version': '1.2.3' }
```

* `beats_repository_url` - The URL of the beats repository, as expected by the package manager
```
# Repository url
beats_repository_url: "https://packages.elastic.co/"
```

* `beats_repository_gpg_key_url` - The URL of the GPG key of the beats repository
```
# GPG key url
beats_repository_gpg_key_url: "https://packages.elastic.co/GPG-KEY-elasticsearch"
```

* `beats_download_url` - If you prefer download a specific version from a remote url instead of installing from repository, you can specify here the base URL where to get the packages from.
```
# Download specific versions of beats from this url (instead of install from repo).
# You can set here a specific url to download the beats packages directly from.
beats_download_url: "https://artifacts.elastic.co/downloads/beats"
```

* `beats_download` - The list of packages to install from a remote url instead of installing from repository. The 'latest' keyword will force to download/install the package, while the 'present' will check the current installed version before trying to download/install the package.
```
# List of specific beats version you want to download from beats_download_url.
# beats_download:
#   - { "name": "filebeat", "version": "5.0.0", "state": "latest" }
#   - { "name": "packetbeat", "version": "5.0.0", "state": "latest" }
#   - { "name": "metricbeat", "version": "5.0.0", "state": "latest" }
```

* `beats_start_*` - These variables control that each beat is started or not after installation and after reboot.
```
# Start beats
beats_start_filebeat: true
beats_start_topbeat: true
beats_start_packetbeat: true
beats_start_metricbeat: true
```

* `beats_restart_*` - These variables control a crontab restarting each beat every hour.
```
# Restart beats every hour
beats_restart_filebeat: false
beats_restart_topbeat: false
beats_restart_packetbeat: false
beats_restart_metricbeat: false
```

* `beats_trace_*` - These variables control a crontab saving the list of files opened by each beats every hour.
```
# Trace beats open files every hour
beats_trace_filebeat: false
beats_trace_topbeat: false
beats_trace_packetbeat: false
beats_trace_metricbeat: false
```

* `beats_init_config_directory` - Where to put the configuration file that is loaded by the beats init scripts. This location will be used to declare environment variables for the beats.
```
# Location of beats configurations loaded by the init script
beats_init_config_directory: "/etc/sysconfig"
```

* `beats_environment` - Here you can set environment variables that will be available in your beats configuration file. A MY_ENV variable will be accessed in your yaml file as a simple shell variable, that is : "${MY_ENV}".
```
# Environment variable to set, they will be available in your beats configuration files
# beats_environment:
#  - { "name": "filebeat", "state": "present", "line": "FB_ENV=$(echo \"test\")" }
```

* `beats_hosts` - This variable allows you to update the /etc/hosts file on remote server, adding or removing lines.
```
# list of host entries to add to or remove from /etc/hosts on beats servers.
# Set state to 'present' to add a line, or 'absent' to remove it.
# beats_hosts:
#   - { "line": "127.0.0.1 localhost elk sample", "state": "absent" }
```

* `beats_ca_cert` - If provided, the contents of this variable will be placed into the file identified by `beats_ca_path` on the target host. You can then include the `beats_ca_path` within your configuration to properly authenticate your TLS connections to Logstash/Elasticsearch/etc.
```
# The contents of this variable will be placed into the `beats_ca_path`
# This should either be set to a string containing your CA certificate or
# use a lookup plugin to retrieve it.
#  ex:
#   beats_ca_cert: "{{ lookup('file', '/path/to/ca.crt') }}"
beats_ca_cert: null
```

* `beats_ca_path` - If a CA certificate is provided in `beats_ca_cert`, it will be created at this path.
```
# Path to which the above certificate will be uploaded
beats_ca_path: /etc/pki/tls/certs/beats.crt
```

* `beats_monit_config_file` - When packages are not available and beats are installed from tarball, we install monit to do process supervision. This is the location of the monit configuration file.
```
# Location of monit configuration file (for beats installed from tarball)
beats_monit_config_file: "/etc/monitrc"
```

* `beats_monit_config_directory` - When packages are not available and beats are installed from tarball, we install monit to do process supervision. This is the location of the monit configuration directory.
```
# Location of monit service files (for beats installed from tarball)
beats_monit_config_directory: "/etc/monit.d"
```

* `beats_config_filebeat` - YAML representation of your filebeat config. This is templated directly into the filebeat.yml configuration file as YAML.
```
# `beats_config_filebeat` is templated directly into filebeat.yml for the config.
# See https://github.com/elastic/beats/blob/master/filebeat/filebeat.yml for
# an exhaustive list of configurations.
beats_config_filebeat:
  filebeat:
    prospectors:
      - paths:
        - /var/log/*.log
        - /var/log/*/*.log
  output:
    file:
      path: /tmp/filebeat
      filename: filebeat.out
  logging:
    to_syslog: true
    level: error
  shipper:
```

* `beats_config_topbeat` - YAML representation of your topbeat config. This is templated directly into the topbeat.yml configuration file as YAML.
```
# `beats_config_topbeat` is templated directly into topbeat.yml for the config.
# See https://github.com/elastic/beats/blob/1.3/topbeat/etc/topbeat.yml for
# an exhaustive list of configurations.
beats_config_topbeat:
  input:
    procs: [".*"]
    stats:
      system: true
      process: true
      filesystem: true
      cpu_per_core: false
  output:
    file:
      path: /tmp/topbeat
      filename: topbeat.out
  logging:
    to_syslog: true
    level: error
  shipper:
```

* `beats_config_packetbeat` - YAML representation of your packetbeat config. This is templated directly into the packetbeat.yml configuration file as YAML.
```
# `beats_config_packetbeat` is templated directly into packetbeat.yml for the config.
# See https://github.com/elastic/beats/blob/master/packetbeat/packetbeat.yml for
# an exhaustive list of configurations.
beats_config_packetbeat:
  interfaces:
    device: any
  protocols:
    dns:
        include_additionals: true
        include_authorities: true
        ports:
        - 53
    http:
        ports:
        - 80
        - 8080
    memcache:
        ports:
        - 11211
    mongodb:
        ports:
        - 27017
    mysql:
        ports:
        - 3306
    pgsql:
        ports:
        - 5432
    redis:
        ports:
        - 6379
    thrift:
        ports:
        - 9090
  output:
    file:
      path: /tmp/packetbeat
      filename: packetbeat.out
  logging:
    to_syslog: true
    level: error
```

* `beats_config_metricbeat` - YAML representation of your metricbeat config. This is templated directly into the metricbeat.yml configuration file as YAML.
```
# `beats_config_metricbeat` is templated directly into metricbeat.yml for the config.
# See https://github.com/elastic/beats/blob/master/metricbeat/metricbeat.yml for
# an exhaustive list of configurations.
beats_config_metricbeat:
  metricbeat.modules:
    - module: system
      metricsets:
        - cpu
        - load
        - filesystem
        - fsstat
        - memory
        - network
        - process
  enabled: true
  period: 10s
  processes: ['.*']
  output:
    file:
      path: /tmp/metricbeat
      filename: metricbeat.out
  logging:
    to_syslog: true
    level: error
```

## Example

To launch this role on the `beats` hosts, run the default playbook.

```
$ ansible-playbook playbook.yml
```

On RedHat/Debian family systems, it will install the Elastic beats listed in `beats_package` from the Elastic beats repository.
You can install beats also listed in `beats_download` directly from remote packages to fetch a specific version.
On other distributions, beats listed in `beats_download` will be installed from the tarball available, as for the RedHat/Debian packages, at the `beats_download_url`.

## Common Configurations

Here is a sample filebeat configuration sending logs on a Logstash instance running on 'elk' server named with IP address 192.168.0.1.
The certificate provided in this example works with the [ELK docker image from sebp](https://hub.docker.com/r/sebp/elk/).

``` yaml
beats:
  - filebeat

beats_package: []

beats_download:
  - { "name": "filebeat", "version": "5.0.2", "state": "present" }

beats_environment:
  - { "name": "filebeat", "state": "present", "line": "TIMEZONE=$(cat /etc/timezone 2>/dev/null || awk -F \"=\" '$1 == \"ZONE\" {gsub(\"\\\"\",\"\",$2); print $2}' /etc/sysconfig/clock 2>/dev/null || ls -l /etc/localtime 2>/dev/null |awk '$(NF-1) == \"->\" {print $NF}' |sed 's/.*\/\([^\/]\+\/[^\/]\+\)$/\1/')" }

beats_hosts:
  - { "line" : "192.168.0.1 elk", "state" : "present" }

beats_ca_path: /etc/pki/tls/certs/logstash-beats.crt
beats_ca_cert: |-
  -----BEGIN CERTIFICATE-----
  MIIC6zCCAdOgAwIBAgIJANPZwuf+5wTLMA0GCSqGSIb3DQEBCwUAMAwxCjAIBgNV
  BAMMASowHhcNMTUxMjI4MTA0NTMyWhcNMjUxMjI1MTA0NTMyWjAMMQowCAYDVQQD
  DAEqMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAp+jHFvhyYKiPXc7k
  0c33f2QV+1hHNyW/uwcJbp5jG82cuQ41v70Z1+b2veBW4sUlDY3yAIEOPSUD8ASt
  9m72CAo4xlwYKDvm/Sa3KJtDk0NrQiz6PPyBUFsY+Bj3xn6Nz1RW5YaP+Q1Hjnks
  PEyQu4vLgfTSGYBHLD4gvs8wDWY7aaKf8DfuP7Ov74Qlj2GOxnmiDEF4tirlko0r
  qQcvBgujCqA7rNoG+QDmkn3VrxtX8mKF72bxQ7USCyoxD4cWV2mU2HD2Maed3KHj
  KAvDAzSyBMjI+qi9IlPN5MR7rVqUV0VlSKXBVPct6NG7x4WRwnoKjTXnr3CRADD0
  4uvbQQIDAQABo1AwTjAdBgNVHQ4EFgQUVFurgDwdcgnCYxszc0dWMWhB3DswHwYD
  VR0jBBgwFoAUVFurgDwdcgnCYxszc0dWMWhB3DswDAYDVR0TBAUwAwEB/zANBgkq
  hkiG9w0BAQsFAAOCAQEAaLSytepMb5LXzOPr9OiuZjTk21a2C84k96f4uqGqKV/s
  okTTKD0NdeY/IUIINMq4/ERiqn6YDgPgHIYvQheWqnJ8ir69ODcYCpsMXIPau1ow
  T8c108BEHqBMEjkOQ5LrEjyvLa/29qJ5JsSSiULHvS917nVgY6xhcnRZ0AhuJkiI
  ARKXwpO5tqJi6BtgzX/3VDSOgVZbvX1uX51Fe9gWwPDgipnYaE/t9TGzJEhKwSah
  kNr+7RM+Glsv9rx1KcWcx4xxY3basG3/KwvsGAFPvk5tXbZ780VuNFTTZw7q3p8O
  Gk1zQUBOie0naS0afype5qFMPp586SF/2xAeb68gLg==
  -----END CERTIFICATE-----
beats_config_filebeat:
  fields:
    timezone: "${TIMEZONE}"
  fields_under_root: true
  filebeat:
    spool_size: 8192
    publish_async: true
    registry_file: /var/lib/filebeat/filebeat.state
    prospectors:
      - paths:
          - /var/log/messages
          - /var/log/secure
          - /var/log/*.log
        document_type: syslog
      - paths:
          - /var/log/nginx/*access.log
          - /var/log/apache*/*access.log
          - /var/log/http*/*access.log
        document_type: http
      - paths:
          - /var/log/mysql.log
          - /var/log/mysqld.log
          - /var/log/mysql/*.log
          - /var/log/mysqld/*.log
          - /var/lib/mysql/*.log
          - /var/lib/mysqld/*.log
        document_type: mysql
  output:
    logstash:
      hosts: ["elk:5044"]
      index: "filebeat"
      ssl:
        certificate_authorities:
          - /etc/pki/tls/certs/logstash-beats.crt
  logging:
    to_syslog: false
    level: error
    to_files: true
    files:
      path: /var/log/filebeat
      name: filebeat.log
      rotateeverybytes: 10485760
      keepfiles: 7
```

## Tests

To test this role on your `beats` hosts, run the tests/playbook.yml playbook.

```
$ ansible-playbook tests/playbook.yml
```

## Limitations

It is known to work on CentOS, Debian and Alpine Linux.
It has been adapted to be run on old Fedora Core 5 too.

## License

BSD

## Authors

* **David Wittman** - Initial release - Installation of Filebeat on CentOS [DavidWittman](https://github.com/DavidWittman/ansible-filebeat)
* **Yann Autissier** - Extends to all Elastic beats on all platforms - [@aya](https://github.com/aya/ansible-beats)
