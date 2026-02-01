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
- **Ignores timestamps and metadata** - only detects actual configuration changes.

---

## Role Variables

| Variable | Description | Type | Required | Default |
|---------|-------------|------|----------|---------|
| `data_store.local.path` | Local path where backup file should be saved | `str` | No | N/A |
| `data_store.local.filename` | Filename for the backup in local path | `str` | No | N/A |
| `data_store.scm.origin.user.name` | Git user name used for SCM commit | `str` | No | N/A |
| `data_store.scm.origin.user.email` | Git user email used for SCM commit | `str` | No | N/A |
| `data_store.scm.origin.url` | URL of the remote Git repository | `str` | Yes (if using SCM) | N/A |
| `data_store.scm.origin.token` | Authentication token for pushing to Git | `str` | Yes (if using SCM HTTPS) | N/A |
| `data_store.scm.origin.filename` | Filename for backup in SCM repo | `str` | Yes (if using SCM) | N/A |
| `data_store.scm.origin.path` | Directory path inside the repo to save backup | `str` | No | N/A |
| `data_store.scm.origin.ssh_key_file` | Path to the SSH private key file for Git authentication | `str` | Yes (if using SCM SSH) | N/A |
| `data_store.scm.origin.ssh_key_content` | The content of the SSH private key | `str` | Yes (if using SCM SSH) | N/A |
| `type` | Type of backup to perform. Options: `"full"`, `"incremental"`, or `"diff"` | `str` | No | `"full"` |


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

### Create Backup and Push to Github via HTTPS

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

### Create Backup and Push to Github via SSH key content

```yaml
- name: Create Network Backup and Push to GitHub
  hosts: network
  gather_facts: false
  tasks:
    - name: Create Network Backup
      ansible.builtin.include_role:
        name: network.backup.backup
      vars:
        github_user: "ansible"
        github_repo: "ansible_ssh"
        git_ssh_private_key: |
          -----BEGIN OPENSSH PRIVATE KEY-----
          <enter your key>
          -----END OPENSSH PRIVATE KEY-----
        data_store:
          scm:
            origin:
              user:
                name: "your_name"
                email: "your_email@example.com"
              url: "git@github.com:{{ github_user }}/{{ github_repo }}.git"
              ssh_key_content: "{{ git_ssh_private_key }}"
              filename: "{{ ansible_date_time.date }}_{{ inventory_hostname }}.txt"
              path: "backups/{{ ansible_date_time.date }}/{{ inventory_hostname }}"
```

### Create Backup and Push to Github via SSH key file

```yaml
- name: Create Network Backup and Push to GitHub
  hosts: network
  gather_facts: false
  tasks:
    - name: Create Network Backup
      ansible.builtin.include_role:
        name: network.backup.backup
      vars:
        github_user: "ansible"
        github_repo: "ansible_ssh"
        git_ssh_private_key_file: "/home/ansible/.ssh/github_private_key"
        data_store:
          scm:
            origin:
              user:
                name: "your_name"
                email: "your_email@example.com"
              url: "git@github.com:{{ github_user }}/{{ github_repo }}.git"
              ssh_key_file: "{{ git_ssh_private_key_file }}"
              filename: "{{ ansible_date_time.date }}_{{ inventory_hostname }}.txt"
              path: "backups/{{ ansible_date_time.date }}/{{ inventory_hostname }}"
```

### Create Differential Backup (Only Publish if Config Changed)

```yaml
- name: Create Network Backup and Push to GitHub
  hosts: network
  gather_facts: false
  tasks:
    - name: Create Network Backup
      ansible.builtin.include_role:
        name: network.backup.backup
      vars:
        type: "diff"  # Enable differential backup
        data_store:
          scm:
            origin:
              user:
                name: "your_name"
                email: "your_email@example.com"
              url: "git@github.com:youruser/your-backup-repo.git"
              ssh_key_file: "/path/to/ssh/key"
              filename: "{{ ansible_date_time.date }}_{{ inventory_hostname }}.txt"
              path: "backups/{{ ansible_date_time.date }}/{{ inventory_hostname }}"
```

> **Note**: With `type: "diff"`, the backup will only be published to SCM if actual configuration changes are detected. Timestamps and metadata differences are ignored. See [Differential Backup Documentation](Differential_Backup_Documentation.md) for more details.

## License

GNU General Public License v3.0 or later.

See [LICENSE](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.

## Author Information

- Ansible Network Content Team
