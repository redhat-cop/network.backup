# network.backup.restore

A role to provide a platform-agnostic way of restoring previously backed-up network configurations across supported platforms such as IOS, IOS-XR, NX-OS, EOS, and Junos.

This role supports restoring from both local and remote (e.g., Git-based) data stores and can intelligently compare the current configuration with the backup before applying changes.

---

## Capabilities

### Restore Configuration
- Restores a previously saved configuration to the device.
- Supports restoration from local file systems or remote Git repositories.
- Optionally compares the current running configuration with the backup and only performs restore if differences are found.

### SHA-256 Hash Verification
- Automatically verifies backup file integrity before restoring using SHA-256 hashes.
- Reads expected hash from `.sha256` file (if available).
- Calculates actual hash of backup file and compares with expected hash.
- Aborts restore operation if hash mismatch is detected (prevents restoring corrupted or tampered files).
- Hash verification is enabled by default but can be disabled if needed.

---

## Role Variables

| Variable | Description | Type | Required | Default |
|---------|-------------|------|----------|---------|
| `data_store.local.path` | Local path where the backup file is located | `str` | No | N/A |
| `data_store.local.filename` | Name of the backup file to restore | `str` | No | N/A |
| `data_store.scm.origin.user.name` | Git user name used for SCM operations | `str` | No | N/A |
| `data_store.scm.origin.user.email` | Git user email used for SCM operations | `str` | No | N/A |
| `data_store.scm.origin.url` | URL of the remote Git repository | `str` | Yes (if using SCM) | N/A |
| `data_store.scm.origin.token` | Authentication token for accessing Git | `str` | Yes (if using SCM HTTPS) | N/A |
| `data_store.scm.origin.filename` | Filename to restore from the Git repo | `str` | Yes (if using SCM) | N/A |
| `data_store.scm.origin.path` | Path in the repo where the file is located | `str` | No | N?A |
| `data_store.scm.origin.ssh_key_file` | Path to the SSH private key file for Git authentication | `str` | Yes (if using SCM SSH) | N/A |
| `data_store.scm.origin.ssh_key_content` | The content of the SSH private key | `str` | Yes (if using SCM SSH) | N/A |
| `verify_backup_hash` | Enable hash verification before restore. When `true`, verifies backup file integrity using SHA-256 hash | `bool` | No | `true` |

> Either `data_store.local` or `data_store.scm` must be provided.

> **Note**: When `verify_backup_hash` is enabled (default), the role automatically looks for a `.sha256` file with the same name as the backup file. For example, if the backup file is `ios_device_backup.txt`, it will look for `ios_device_backup.txt.sha256` in the same directory. If the hash file exists, the role verifies the backup file integrity before restoring. If the hashes don't match, the restore operation is aborted for safety.

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

### Restore Network Configuration from SCM HTTPS

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

### Restore Network Configuration from SCM SSH key content

```yaml
- name: Restore Network Configuration from GitHub
  hosts: network
  gather_facts: false
  tasks:
    - name: Run restore network config
      ansible.builtin.include_role:
        name: network.backup.restore
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
              url: "git@github.com:{{ github_user }}/{{ github_repo }}.git"
              ssh_key_content: "{{ git_ssh_private_key }}"
              filename: "{{ ansible_date_time.date }}_{{ inventory_hostname }}.txt"
              path: "backups/{{ ansible_date_time.date }}/{{ inventory_hostname }}"
```


### Restore Network Configuration from SCM SSH key file

```yaml
- name: Restore Network Configuration from GitHub
  hosts: network
  gather_facts: false
  tasks:
    - name: Run restore network config
      ansible.builtin.include_role:
        name: network.backup.restore
      vars:
        github_user: "ansible"
        github_repo: "ansible_ssh"
        git_ssh_private_key_file: "/home/ansible/.ssh/github_private_key"
        data_store:
          scm:
            origin:
              url: "git@github.com:{{ github_user }}/{{ github_repo }}.git"
              ssh_key_file: "{{ git_ssh_private_key_file }}"
              filename: "{{ ansible_date_time.date }}_{{ inventory_hostname }}.txt"
              path: "backups/{{ ansible_date_time.date }}/{{ inventory_hostname }}"
```

### Restore with Hash Verification

```yaml
- name: Restore Network Configuration with Hash Verification
  hosts: network
  gather_facts: false
  tasks:
    - name: Run restore network config
      ansible.builtin.include_role:
        name: network.backup.restore
      vars:
        verify_backup_hash: true  # Enable hash verification (default)
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

> **Note**: When `verify_backup_hash: true` (default), the restore role automatically verifies the backup file integrity before restoring. It looks for a `.sha256` file with the same name as the backup file and compares the expected hash with the actual hash of the backup file. If the hashes don't match, the restore operation is aborted to prevent restoring corrupted or tampered configurations.

### Disable Hash Verification (Not Recommended)

```yaml
- name: Restore Network Configuration without Hash Verification
  hosts: network
  gather_facts: false
  tasks:
    - name: Run restore network config
      ansible.builtin.include_role:
        name: network.backup.restore
      vars:
        verify_backup_hash: false  # Disable hash verification
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

> **Warning**: Disabling hash verification is not recommended for production environments. Hash verification ensures backup file integrity and prevents restoring corrupted or tampered configurations.

## License

GNU General Public License v3.0 or later.

See [LICENSE](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.

## Author Information

- Ansible Network Content Team