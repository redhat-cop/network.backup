
# Ansible Network Backup And Restore Manager.
[![CI](https://zuul-ci.org/gated.svg)](https://dashboard.zuul.ansible.com/t/ansible/builds?project=ansible-collections%2Fansible.network) <!--[![Codecov](https://img.shields.io/codecov/c/github/ansible-collections/ansible.network)](https://codecov.io/gh/ansible-collections/ansible.network)-->

This role provides a single platform-agnostic entry management for backups and restore specific use cases.

## Using the platform-agnostic role network.brm.run as part of a network.brm collection.

**Capabilities**
```
`Differential backup`:
- This enables user to backup configuration only when there has been some change since last time we did a backup.
- User can use this operation to get differential backup and save the backed-up files to either local data-store or to GitHub repository
- User can also push backup files on to the GitHub with tags.

`Full Backup`:
- This enables user to running-configuration from device and save the backup to local or remote data-store
- User can also push backup files on to the GitHub with tags.

- `Differential Restore`:
- Detect the diff and restore network configurations read from local data store or GitHub repository if any diff found.

`Full Restore`:
- Restore network configurations read from local data store or GitHub Repository.

```
# Examples

## Using Differemtial  Backup operation

#### fetch running config and saves the backup to local data-store.
```yaml
run.yml
---
- hosts: rtr1
  gather_facts: true
  tasks:
    - name: Network Backup and Resource Manager
      ansible.builtin.include_role:
        name: network.brm.run
      vars:
        operation: backup
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
        name: network.brm.run
      vars:
        operation: backup
        data_store:
          scm:  
            origin:
              url: "{{ GIT_REPO }}"
              token: "{{ GH_ACCESS_TOKEN }}"
              user:
                name: "{{ username }}"
                email: "{{ email }}"
```

## Using Restore Operation

#### Restore the config backed up in local data store by overriding existing config.
```yaml
run.yml
---
- hosts: rtr1
  gather_facts: true
  tasks:
    - name: Network Backup and Resource Manager
      ansible.builtin.include_role:
        name: network.brm.run
      vars:
        operation: restore
        data_store:
          local: "./network_local_backup/network"
```

#### Restore the config backed up in GitHub repository only if diff exists.
```yaml
run.yml
---
- hosts: rtr1
  gather_facts: true
  tasks:
    - name: Network Backup and Resource Manager
      ansible.builtin.include_role:
        name: network.brm.run
      vars:
        operation: restore
        data_store:
          scm:  
            origin:
              url: "{{ GIT_REPO }}"
              token: "{{ GH_ACCESS_TOKEN }}"
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
