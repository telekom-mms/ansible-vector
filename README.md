[![molecule](https://github.com/telekom-mms/ansible-vector/actions/workflows/molecule.yml/badge.svg)](https://github.com/telekom-mms/ansible-vector/actions/workflows/molecule.yml) [![ansible-lint](https://github.com/telekom-mms/ansible-vector/actions/workflows/ansible-lint.yml/badge.svg)](https://github.com/telekom-mms/ansible-vector/actions/workflows/ansible-lint.yml)

# ansible-vector

This is a ansible collection to set up [vector](https://vector.dev) on various systems.

## supported & tested OS

| OS           | Tested
|--------------|--------------------
| Debian 12    | :white_check_mark:
| RL8          | :white_check_mark:
| RL9          | :white_check_mark:
| Ubuntu 22.04 | :white_check_mark:
| Ubuntu 24.04 | :white_check_mark:

## Variables

| Variable                      | Required | Default                                          | Description
|-------------------------------|----------|--------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------
| vector_template               | yes      | vector.yaml.j2                                   | path of your vector.yaml template
| vector_config_file            | yes      | /etc/vector/vector.yaml                          | system path of your vector.yaml configuration
| vector_groups                 | no       |                                                  | add user vector to specified groups
| vector_install_from_repo      | no       | false                                            | whether to install vector from packages or install from deb or redhat based repositories
| vector_repo_key               | no       | see `defaults/main.yml`                          | configurable repo key, in case repo proxy is used
| vector_repo                   | no       | see `defaults/main.yml`                          | configurable repo, in case repo proxy is used
| vector_restartsec             | no       |                                                  | add RestartSec to systemd config
| vector_restartsec_template    | no       | vector_restartsec.j2                             | path of your vector_restartsec template
| vector_restartsec_config_file | no       | /etc/systemd/system/vector.service.d/vector.conf | system path of your drop-in vector systemd configuration
| vector_package                | no       | vector                                           | option to define vector version with package name
| vector_version                | no       |                                                  | define vector version while vector is installed by source
| sources                       | yes      | false                                            | ingest observability data from a wide variety of targets [link](https://vector.dev/docs/reference/configuration/sources/)
| transforms                    | no       | false                                            | shape your data as it moves through your Vector topology [link](https://vector.dev/docs/reference/configuration/transforms/)
| sinks                         | yes      | false                                            | deliver your observability data to a variety of destinations [link](https://vector.dev/docs/reference/configuration/sinks/)

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
