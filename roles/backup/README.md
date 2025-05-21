# network.backup.backup

A role to provide a platform-agnostic way of managing network configuration backups across supported platforms such as IOS, IOS-XR, NX-OS, EOS, and Junos.

This role supports full and differential backups, storing them locally or in a remote SCM repository (e.g., GitHub).

---

## Capabilities

### Full Backup
- Retrieves the complete running configuration from the network device.
- Stores the backup locally or remotely (e.g., GitHub).
- Allows tagging and organizing backups by platform, date, or hostname.

### Differential Backup
- Backs up the configuration **only if** there are changes compared to the last saved version.
- Works with both local and Git-based data stores.
- Helps reduce storage and SCM noise by saving only when diff exists.

---

## Role Variables

| Variable | Description | Type | Required | Default |
|---------|-------------|------|----------|---------|
| `data_store.local.path` | Local path where backup file should be saved | `str` | No | N/A |
| `data_store.local.filename` | Filename for the backup in local path | `str` | No | N/A |
| `data_store.scm.origin.user.name` | Git user name used for SCM commit | `str` | No | N/A |
| `data_store.scm.origin.user.email` | Git user email used for SCM commit | `str` | No | N/A |
| `data_store.scm.origin.url` | URL of the remote Git repository | `str` | Yes (if using SCM) | N/A |
| `data_store.scm.origin.token` | Authentication token for pushing to Git | `str` | Yes (if using SCM) | N/A |
| `data_store.scm.origin.filename` | Filename for backup in SCM repo | `str` | Yes (if using SCM) | N/A |
| `data_store.scm.origin.path` | Directory path inside the repo to save backup | `str` | No | N/A |

> Note: Either `data_store.local` or `data_store.scm` must be provided.

---

## Usage Examples

### Create Backup Locally

```yaml
- name: Create Network Backup Locally
  hosts: network
  gather_facts: false
  tasks:
    - name: Create Network Backup
      ansible.builtin.include_role:
        name: network.backup.backup
      vars:
        data_store:
          local:
            path: "/tmp/network_backups/{{ inventory_hostname }}"
            filename: "{{ ansible_date_time.date }}_{{ inventory_hostname }}.txt"
```

## Usage Examples

### Create Backup and Push to Github

```yaml
- name: Create Network Backup and Push to GitHub
  hosts: network
  gather_facts: false
  tasks:
    - name: Create Network Backup
      ansible.builtin.include_role:
        name: network.backup.backup
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