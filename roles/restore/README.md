# network.backup.restore

A role to provide a platform-agnostic way of restoring previously backed-up network configurations across supported platforms such as IOS, IOS-XR, NX-OS, EOS, and Junos.

This role supports restoring from both local and remote (e.g., Git-based) data stores and can intelligently compare the current configuration with the backup before applying changes.

---

## Capabilities

### Restore Configuration
- Restores a previously saved configuration to the device.
- Supports restoration from local file systems or remote Git repositories.
- Optionally compares the current running configuration with the backup and only performs restore if differences are found.

---

## Role Variables

| Variable | Description | Type | Required | Default |
|---------|-------------|------|----------|---------|
| `data_store.local.path` | Local path where the backup file is located | `str` | No | N/A |
| `data_store.local.filename` | Name of the backup file to restore | `str` | No | N/A |
| `data_store.scm.origin.user.name` | Git user name used for SCM operations | `str` | No | N/A |
| `data_store.scm.origin.user.email` | Git user email used for SCM operations | `str` | No | N/A |
| `data_store.scm.origin.url` | URL of the remote Git repository | `str` | Yes (if using SCM) | N/A |
| `data_store.scm.origin.token` | Authentication token for accessing Git | `str` | Yes (if using SCM) | N/A |
| `data_store.scm.origin.filename` | Filename to restore from the Git repo | `str` | Yes (if using SCM) | N/A |
| `data_store.scm.origin.path` | Path in the repo where the file is located | `str` | No | N?A |

> Either `data_store.local` or `data_store.scm` must be provided.

---

## Usage Examples

### Restore from Local Datastore

```yaml
- name: Restore Network Configuration from Local Path
  hosts: network
  gather_facts: false
  tasks:
    - name: Run restore network config
      ansible.builtin.include_role:
        name: network.backup.restore
      vars:
        data_store:
          local:
            path: "/tmp/network_backups/{{ inventory_hostname }}"
            filename: "{{ ansible_date_time.date }}_{{ inventory_hostname }}.txt"
```

### Restore Network Configuration from SCM

```yaml
- name: Restore Network Configuration from GitHub
  hosts: network
  gather_facts: false
  tasks:
    - name: Run restore network config
      ansible.builtin.include_role:
        name: network.backup.restore
      vars:
        data_store:
          scm:
            origin:
              user:
                name: "your_name"
                email: "your_email@example.com"
              url: "https://github.com/youruser/your-backup-repo"
              token: "{{ gh_token }}"
              filename: "{{ ansible_date_time.date }}_{{ inventory_hostname }}.txt"
              path: "backups/{{ ansible_date_time.date }}/{{ inventory_hostname }}"
```
## License

GNU General Public License v3.0 or later.

See [LICENSE](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.

## Author Information

- Ansible Network Content Team