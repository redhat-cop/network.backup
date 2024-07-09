# backup.

A role to provide platform-agnostic way of managing network backup on supported network platforms.

## Using the platform-agnostic role network.brm.run as part of a network.brm collection.

**Capabilities**
```
**Full Backup**:
- Allows users to retrieve the running network configuration from the device and save it to a local or remote data store.
- Users can also upload backup files to GitHub, with the option to add tags for better organization.

**Differential Backup**:
- Enables users to back up the network configuration only if there have been changes since the last backup.
- Users can perform a differential backup and save the updated files either to a local data store or a GitHub repository, with the option to add tags for better organization.store network configurations read from local data store or GitHub Repository.
```
