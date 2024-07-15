# Network Backup Experience

## Description

This experience is designed to facilitate the automated backup of network configurations using Ansible. It provides a streamlined way to seed the necessary resources and create, manage, and execute backup tasks, ensuring that network configurations are safely stored and can be easily restored if needed.

## What This Experience Covers

This experience includes the following key components:

### Project Templates

- **Backup Project Template**: Defined within the `setup.yaml` playbook, this template helps organize and manage all necessary components for the network backup experience. It ensures that relevant files, roles, and configurations are logically arranged, making it easier to maintain and execute automation tasks.

### Execution Environment (EE)

- **Custom EE Definitions**: A tailored execution environment that ensures compatibility and functionality of all necessary Ansible modules and dependencies required for network backup tasks.

### Job Templates

- **Create Full Backup Job Template**: Pre-configured to execute the network backup playbook, streamlining the process of creating full backups of network configurations.
- **Create Differential Backup Job Template**: Pre-configured to execute the network backup playbook, streamlining the process of creating differential backups when differences exist between the running configuration and previous backups.

### Playbooks

- **Playbooks**: Located in the playbooks directory, these scripts are specifically designed to connect to network devices, retrieve configuration data, and store it in a designated backup location.

### Surveys

- **Backup Surveys**: Defined within the `backup.j2` file, backup surveys provide a dynamic interaction mechanism for users to specify parameters for the backup jobs. These surveys enable users to:
  - Choose the type of backup to be created (full, differential)
  - Define the location for storing the backups
  - Set the backup frequency (Hourly, Daily, Weekly, Monthly)

## Resources Created by This Experience

1. **Project Templates**
    - Ensure that all relevant files, roles, and configurations are logically arranged, facilitating easier maintenance and execution of automation tasks.

2. **Execution Environment (EE)**
    - A customized execution environment is created to run the network backup tasks, ensuring all necessary dependencies are included.

3. **Job Templates**
    - Outline the necessary parameters and configurations to perform network backups using the provided playbooks.

## How to Use

1. **Use Seed Red Hat Experience Job**
    - Ensure the custom EE is correctly built and available in your Ansible Automation PlatformExecute the "Seed Red Hat Experience" job within the Ansible Automation Platform, and select the "Network" category to load this experience.

2. **Use the Job Templates**
    - In the `Network Backup Automation Experience Project` execute the required job templates to perform network backups. Monitor the job execution and verify that the backups are correctly stored.

## Contribution

Contributions to this project are welcome. Please fork the repository, make your changes, and submit a pull request.

## License

GNU General Public License v3.0 or later.

See [LICENSE](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.This project is licensed under the MIT License. See the [LICENSE](https://github.com/rohitthakur2590/network.backup/blob/main/LICENSE) file for details.

---

For any issues or questions, please open an issue on the [GitHub issue tracker](https://github.com/rohitthakur2590/network.backup/issues).
