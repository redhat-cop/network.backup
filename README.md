
# Ansible Network Backup
[![CI](https://github.com/ansible-network/network.backup/actions/workflows/tests.yml/badge.svg?event=schedule)](https://github.com/ansible-network/network.backup/actions/workflows/tests.yml)
[![OpenSSF Best Practices](https://bestpractices.coreinfrastructure.org/projects/7404/badge)](https://bestpractices.coreinfrastructure.org/projects/7404)


## About
- Ansible Network Backup Collection contains the role `backup` which provides a platform-agnostic way of managing network backup on supported network platforms. This collection provides the user the capabilities to create, compare and tag backups supporting local and remote data stores.

- This collection contains another role `restore` that provides a platform-agnostic way of managing network restores on supported network platforms. This collection provides the user the capabilities to fetch backups from remote or local data stores and perform restore config.

- Network Backup collection can be used by anyone who are looking to manage and maintain network infrastructure, automate the backup and restore process, and ensure data is regularly and securely backed up and available to restore when required. This includes system administrators and IT professionals.

## Included content

Click on the name of a role, playbook, or rulebook to view that content's documentation:

<!--start collection content-->
### Roles
Name | Description
--- | ---
[network.backup.backup](roles/backup/README.md)|A platform agnostic role to manage network backup operations.
[network.backup.restore](roles/restore/README.md)|A platform agnostic role to manage network restore operations.
<!--end collection content-->

## Requirements
- [Requires Ansible](https://github.com/redhat-cop/network.backup/blob/main/meta/runtime.yml)
- [Requires Content Collections](https://github.com/redhat-cop/network.backup/blob/main/galaxy.yml#L5https://forum.ansible.com/c/news/5/none)
- [Testing Requirements](https://github.com/redhat-cop/network.backup/blob/main/test-requirements.txt)
- Users also need to include platform collections as per their requirements. The supported platform collections are:
  - [arista.eos](https://github.com/ansible-collections/arista.eos) >= v9.0.0
  - [cisco.ios](https://github.com/ansible-collections/cisco.ios) >= v8.0.0
  - [cisco.iosxr](https://github.com/ansible-collections/cisco.iosxr) >= v9.0.0
  - [cisco.nxos](https://github.com/ansible-collections/cisco.nxos) >= v8.0.0
  - [junipernetworks.junos](https://github.com/ansible-collections/junipernetworks.junos) >= v8.0.0


## Installation
To consume this Validated Content from Automation Hub, the following needs to be added to ansible.cfg:
```
[galaxy]
server_list = automation_hub

[galaxy_server.automation_hub]
url=https://console.redhat.com/api/automation-hub/content/validated/
auth_url=https://sso.redhat.com/auth/realms/redhat-external/protocol/openid-connect/token
token=<SuperSecretToken>
```

Utilize the current Token, and if the token has expired, obtain the necessary
token from the [Automation Hub Web UI](https://console.redhat.com/ansible/automation-hub/token).

With this configured, simply run the following commands:

```
ansible-galaxy collection install network.base
ansible-galaxy collection install network.backup
```

## Use Cases

**Full Backup**:
- Allows users to retrieve the running network configuration from the device and save it to a local or remote data store.
- Users can also upload backup files to GitHub, with the option to add tags for better organization.

**Differential Backup**:
- Enables users to back up the network configuration only if there have been changes since the last backup.
- Users can perform a differential backup and save the updated files either to a local data store or a GitHub repository, with the option to add tags for better organization.

**Restore Configuration**:
- Allows users to restore a previously backed-up configuration.
- Users can compare the running configuration with the backup to identify differences and restore the configuration only if differences are found.


### Full Network Backup

#### fetch running config and saves the backup to the local data store.
```yaml
run.yml
---
- hosts: rtr1
  gather_facts: true
  tasks:
    - name: Network Backup and Resource Manager
      ansible.builtin.include_role:
        name: network.backup.backup
      vars:
        operation: backup
        type: full
        data_store:
          local: "./network_local_backup/network"
```

#### fetch running config and publish the backup to a remote repository.
```yaml
run.yml
---
- hosts: rtr1
  gather_facts: true
  tasks:
    - name: Network Backup and Resource Manager
      ansible.builtin.include_role:
        name: network.backup.backup
      vars:
        operation: backup
        type: full
        data_store:
          scm:  
            origin:
              url: "{{ GIT_REPO }}"
              token: "{{ GH_ACCESS_TOKEN }}"
              user:
                name: "{{ username }}"
                email: "{{ email }}"
```

### Differential Network Backup

#### fetch running config and saves the backup to the local data store.
```yaml
run.yml
---
- hosts: rtr1
  gather_facts: true
  tasks:
    - name: Network Backup and Resource Manager
      ansible.builtin.include_role:
        name: network.backup.backup
      vars:
        operation: backup
        type: diff
        data_store:
          local: "./network_local_backup/network"
```

#### fetch running config and publish the backup to a remote repository.
```yaml
run.yml
---
- hosts: rtr1
  gather_facts: true
  tasks:
    - name: Network Backup and Resource Manager
      ansible.builtin.include_role:
        name: network.backup.backup
      vars:
        operation: backup
        type: diff
        data_store:
          scm:  
            origin:
              url: "{{ GIT_REPO }}"
              token: "{{ GH_ACCESS_TOKEN }}"
              tag: default 
              user:
                name: "{{ username }}"
                email: "{{ email }}"
```

#### Fetch backup and restore a network appliance's configuration.
```yaml
run.yml
---
- name: Restore config
  hosts: ios
  gather_facts: true

  tasks:
    - name: Restore from locally backed up config
      ansible.builtin.include_role:
        name: network.backup.restore
      vars:
        operation: restore
        data_store:
          local: "{{ network_backup_path }}"
```

#### Fetch backup from remote repo and restore to a network appliance's configuration.
```yaml
run.yml
---
- name: Restore config
  hosts: ios
  gather_facts: true

  tasks:
    - name: Restore config fetched from remote data store
      ansible.builtin.include_role:
        name: network.backup.restore
      vars:
        operation: restore
        data_store:
          scm:
            origin:
              url: "{{ github_repo }}"
              user:
                name: "{{ github_username }}"
                email: "{{ user_email }}"
              token: "{{ token }}"
              path: "{{ path_to_backup_file }}"
```

## Testing

The project uses tox to run `ansible-lint` and `ansible-test sanity`.
Assuming this repository is checked out in the proper structure,
e.g. `collections_root/ansible_collections/network/backup`, run:

```shell
  tox -e ansible-lint
  tox -e py39-sanity
```

To run integration tests, ensure that your inventory has a `network_base` group.
Depending on what test target you are running, comment out the host(s).

```shell
[network_hosts]
ios
junos

[ios:vars]
< enter inventory details for this group >

[junos:vars]
< enter inventory details for this group >
```

```shell
  ansible-test network-integration -i /path/to/inventory --python 3.9 [target]
```

# Ansible Network Restore

[![CI](https://github.com/redhat-cop/network.restore/actions/workflows/tests.yml/badge.svg?branch=main)](https://github.com/redhat-cop/network.restore/actions/workflows/tests.yml)[![OpenSSF Best Practices](https://bestpractices.coreinfrastructure.org/projects/8823/badge)](https://bestpractices.coreinfrastructure.org/projects/8823)

This repository contains the `network.restore` Ansible Collection.

## Installation
To consume this Validated Content from Automation Hub, the following needs to be added to ansible.cfg:
```
[galaxy]
server_list = automation_hub

[galaxy_server.automation_hub]
url=https://console.redhat.com/api/automation-hub/content/published/
auth_url=https://sso.redhat.com/auth/realms/redhat-external/protocol/openid-connect/token
token=<SuperSecretToken>
```

Get the required token from the [Automation Hub Web UI](https://console.redhat.com/ansible/automation-hub/token).

With this configured, simply run the following commands:

```
ansible-galaxy collection install network.base
ansible-galaxy collection install network.restore
```

## Use Cases

#### Fetch backup and restore a network appliance's configuration.
```yaml
run.yml
---
- name: The network.restore play
  hosts: ios
  gather_facts: true

  tasks:
    - name: The network restore task
      ansible.builtin.include_role:
        name: network.restore.run
      vars:
        operation: restore
        data_store:
          local: "{{ network_backup_path }}"
```

#### Fetch backup from remote repo and restore a network appliance's configuration.
```yaml
run.yml
---
- name: The network.restore play
  hosts: ios
  gather_facts: true

  tasks:
    - name: The network restore task
      ansible.builtin.include_role:
        name: network.restore.run
      vars:
        operation: restore
        data_store:
          scm:
            origin:
              url: "{{ github_repo }}"
              user:
                name: "{{ github_username }}"
                email: "{{ user_email }}"
              token: "{{ token }}"
              path: "{{ path_to_backup_file }}"
```

## Testing

The project uses tox to run `ansible-lint` and `ansible-test sanity`.
Assuming this repository is checked out in the proper structure,
e.g. `collections_root/ansible_collections/network/restore`, run:

```shell
  tox -e ansible-lint
  tox -e py39-sanity
```

To run integration tests, ensure that your inventory has a `network_restore` group.
Depending on what test target you are running, comment out the host(s).

```shell
[network_hosts]
ios
nxos

[ios:vars]
< enter inventory details for this group >

[nxos:vars]
< enter inventory details for this group >
```

```shell
  ansible-test network-integration -i /path/to/inventory --python 3.9 [target]
```

## Contributing

We welcome community contributions to this collection. If you find problems, please open an issue or create a PR against this repository.

Don't know how to start? Refer to the [Ansible community guide](https://docs.ansible.com/ansible/devel/community/index.html)!

Want to submit code changes? Take a look at the [Quick-start development guide](https://docs.ansible.com/ansible/devel/community/create_pr_quick_start.html).

We also use the following guidelines:

* [Collection review checklist](https://docs.ansible.com/ansible/devel/community/collection_contributors/collection_reviewing.html)
* [Ansible development guide](https://docs.ansible.com/ansible/devel/dev_guide/index.html)
* [Ansible collection development guide](https://docs.ansible.com/ansible/devel/dev_guide/developing_collections.html#contributing-to-collections)

### Code of Conduct
This collection follows the Ansible project's
[Code of Conduct](https://docs.ansible.com/ansible/devel/community/code_of_conduct.html).
Please read and familiarize yourself with this document.

## Release notes

Release notes are available [here](https://github.com/redhat-cop/network.restore/blob/main/CHANGELOG.rst).

## Related information

- [Developing network resource modules](https://github.com/ansible-network/networking-docs/blob/main/rm_dev_guide.md)
- [Ansible Networking docs](https://github.com/ansible-network/networking-docs)
- [Ansible Collection Overview](https://github.com/ansible-collections/overview)
- [Ansible Roles overview](https://docs.ansible.com/ansible/2.9/user_guide/playbooks_reuse_roles.html)
- [Ansible User guide](https://docs.ansible.com/ansible/latest/user_guide/index.html)
- [Ansible Developer guide](https://docs.ansible.com/ansible/latest/dev_guide/index.html)
- [Ansible Community Code of Conduct](https://docs.ansible.com/ansible/latest/community/code_of_conduct.html)

## Licensing

GNU General Public License v3.0 or later.

See [LICENSE](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.
