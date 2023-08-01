
# Ansible Network Backup
[![CI](https://github.com/ansible-network/network.base/actions/workflows/tests.yml/badge.svg?event=schedule)](https://github.com/ansible-network/network.base/actions/workflows/tests.yml)
[![OpenSSF Best Practices](https://bestpractices.coreinfrastructure.org/projects/7404/badge)](https://bestpractices.coreinfrastructure.org/projects/7404)

Ansible Network Backup collection platform-agnostic single entry point management for network backup.

## Using the platform-agnostic role network.backup.run as part of a network.backup collection.

## Capabilities
`Full Backup`:
- This enables the user to fetch running configuration from the device and save the backup to local or remote data-store
- Users can also push backup files onto GitHub with tags.
 
`Differential backup`:
- This enables users to backup configuration only when there has been some change since the last time we did a backup.
- Users can use this operation to get differential backup and save the backed-up files to either the local data store or to the GitHub repository
- Users can also push backup files onto GitHub with tags.

## Examples

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
        name: network.backup.run
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
        name: network.backup.run
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
        name: network.backup.run
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
        name: network.backup.run
      vars:
        operation: backup
        type: full
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

### See Also:

* [Ansible Using roles](https://docs.ansible.com/ansible/2.9/user_guide/playbooks_reuse_roles.html) for more details.

## Advantages of Using this Role
Provide a single platform agnostics entry point to manage network backup and restore use cases.

### Code of Conduct
This collection follows the Ansible project's
[Code of Conduct](https://docs.ansible.com/ansible/devel/community/code_of_conduct.html).
Please read and familiarize yourself with this document.


## More information

- [Developing network resource modules](https://docs.ansible.com/ansible/latest/network/dev_guide/developing_resource_modules_network.html#developing-resource-modules)
- [Ansible network resources](https://docs.ansible.com/ansible/latest/network/getting_started/network_resources.html)
- [Ansible Collection Overview](https://github.com/ansible-collections/overview)
- [Ansible Roles overview](https://docs.ansible.com/ansible/2.9/user_guide/playbooks_reuse_roles.html)
- [Ansible User guide](https://docs.ansible.com/ansible/latest/user_guide/index.html)
- [Ansible Developer guide](https://docs.ansible.com/ansible/latest/dev_guide/index.html)
- [Ansible Community Code of Conduct](https://docs.ansible.com/ansible/latest/community/code_of_conduct.html)

## Licensing

GNU General Public License v3.0 or later.

See [LICENSE](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.
