![publish](https://github.com/telekom-mms/ansible-vector/workflows/publish/badge.svg)

# ansible-vector

This is a ansible collection to set up [vector](https://vector.dev) on debian and redhat based systems.
It translates the YAML configuration to TOML, so any configuration is possible.

Currently only amd64, arch64, arch7 through deb and rpm packages are supported.

## Variables

| Variable                 | Required | Default                 | Description
|--------------------------|----------|-------------------------|------------
| vector_template          | yes      | vector.yaml.j2          | path of your vector.yaml template
| vector_config_file       | yes      | /etc/vector/vector.yaml | system path of your vector.yaml configuration
| vector_groups            | no       |                         | add user vector to specified groups
| vector_install_from_repo | no       | false                   | whether to install vector from packages or install from deb or redhat based repositories
| vector_repo_key          | no       | see `vars/*.yml`        | configurable repo key, in case repo proxy is used
| vector_repo              | no       | see `vars/*.yml`        | configurable repo, in case repo proxy is used
| vector_package           | no       | vector                  | option to define vector version with package name
| vector_version           | no       |                         | define vector version while vector is installed by source
| sources                  | yes      | false                   | ingest observability data from a wide variety of targets [link](https://vector.dev/docs/reference/configuration/sources/)
| transforms               | no       | false                   | shape your data as it moves through your Vector topology [link](https://vector.dev/docs/reference/configuration/transforms/)
| sinks                    | yes      | false                   | deliver your observability data to a variety of destinations [link](https://vector.dev/docs/reference/configuration/sinks/)

## Example for configuration with ansible
```yaml
sources:
  journald:
    type: journald
    current_boot_only: true

transforms:
  grok:
    type: grok_parser
    inputs:
      - journald
    pattern: '(?<capture>\\d+)%{GREEDYDATA}'

sinks:
  vector:
    type: vector
    inputs: ["journald"]
    address: "vector.example.com:9000"
```

## Example playbook
```yaml
- name: Install and configure vector
  hosts: all
  vars:
    sources:
      journald:
        type: journald
        current_boot_only: true
    transforms:
      grok:
        type: grok_parser
        inputs:
          - journald
        pattern: '(?<capture>\\d+)%{GREEDYDATA}'
    sinks:
      vector:
        type: vector
        inputs: ["journald"]
        address: "vector.example.com:9000"
  roles:
    - telekom_mms.vector.vector
```
