
# Ansible Network Backup
[![CI](https://github.com/ansible-network/network.backup/actions/workflows/tests.yml/badge.svg?event=schedule)](https://github.com/ansible-network/network.backup/actions/workflows/tests.yml)
[![OpenSSF Best Practices](https://bestpractices.coreinfrastructure.org/projects/7404/badge)](https://bestpractices.coreinfrastructure.org/projects/7404)


## About
- The Ansible Network Backup Validated Content provides a comprehensive solution for managing network backups and restores across supported network platforms. This validated content offers two key functionalities: `backup` and `restore`, each designed to be platform-agnostic and user-friendly.

- The `backup` role allows users to create, compare, and tag backups, supporting both local and remote data stores. This ensures that network configurations are regularly and securely backed up, providing a reliable method to safeguard network infrastructure. The role includes SHA-256 hash verification to ensure backup file integrity and detect tampering or corruption.

- The `restore` role enables users to fetch backups from local or remote data stores and perform configuration restores. This functionality ensures that network configurations can be swiftly and accurately restored when needed, minimizing downtime and maintaining network stability. The role automatically verifies backup file integrity using SHA-256 hashes before restoring, preventing corrupted or tampered configurations from being applied.

- The Network Backup Content is ideal for system administrators and IT professionals who need to manage and maintain network infrastructure, automate the backup and restore process, and ensure data is regularly and securely backed up and available for restoration as required.

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
- Automatically verifies backup file integrity using SHA-256 hashes before restoring, ensuring corrupted or tampered files are not applied.

**Hash Verification**:
- Calculates SHA-256 hashes for all backup files to ensure data integrity.
- Stores hash files alongside backup files for easy verification.
- Automatically verifies backup file integrity during restore operations.
- Prevents restoring corrupted or tampered configurations.

## Testing

The project uses tox to run `ansible-lint` and `ansible-test sanity`.
Assuming this repository is checked out in the proper structure,
e.g. `collections_root/ansible_collections/network/backup`, run:

```shell
  tox -e ansible-lint
  tox -e py39-sanity
```

To run integration tests, ensure that your inventory has a `network_backup` group.
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

Release notes are available [here](https://github.com/redhat-cop/network.backup/blob/main/CHANGELOG.rst).

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
