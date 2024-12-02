# Network Restore pattern

## Description

This pattern is designed to facilitate the automated **restore** of network configurations using Ansible. It provides a streamlined way to seed the necessary resources and create, manage, and execute restore tasks, ensuring that network configurations can be efficiently recovered to maintain operational continuity.

---

## What This pattern Covers

This pattern includes the following key components:

### Project Templates

- **Restore Project Template**: Defined within the `setup.yml` playbook, this template helps organize and manage all necessary components for the network restore pattern. It ensures that relevant files, roles, and configurations are logically arranged, making it easier to maintain and execute automation tasks.

### Job Templates

- **Restore Full Configuration Job Template**: Pre-configured to execute the network restore playbook, simplifying the process of restoring the entire network configuration from a previous full backup.

### Playbooks

- **Playbooks**: Located in the `playbooks` directory, these scripts are specifically designed to connect to network devices, retrieve necessary data, and apply configuration changes to restore the network.

### Surveys

- **Restore Surveys**: Defined within the `restore.yaml` file, restore surveys provide a dynamic interaction mechanism for users to specify parameters for the restore jobs. These surveys enable users to:
  - Select the backup version to restore from.
  - Specify devices or configurations to restore.

---

## Resources Created by This pattern

1. **Project Templates**
    - Ensure that all relevant files, roles, and configurations are logically arranged, facilitating easier maintenance and execution of automation tasks.

2. **Job Templates**
    - Outline the necessary parameters and configurations to perform network restores using the provided playbooks.

---

## How to Use

1. **Use Seed Red Hat pattern Job**
    - Ensure the custom EE is correctly built and available in your Ansible Automation Platform.
    - Execute the "Seed Red Hat pattern" job within the Ansible Automation Platform, and select the "Network" category to load this pattern.

2. **Use the Job Templates**
    - In the `Network Operations / Backup & Restore Project`, execute the required job templates to restore network configurations. Monitor the job execution and verify that the configurations are correctly applied.

---

## Contribution

Contributions to this project are welcome. Please fork the repository, make your changes, and submit a pull request.

---

## License

GNU General Public License v3.0 or later.

See [LICENSE](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text. This project is licensed under the MIT License. See the [LICENSE](https://github.com/redhat-cop/network.restore/blob/main/LICENSE) file for details.

---

For any issues or questions, please open an issue on the [GitHub issue tracker](https://github.com/redhat-cop/network.restore/issues).
