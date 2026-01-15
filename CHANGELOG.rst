=======================================
Network Backup Collection Release Notes
=======================================

.. contents:: Topics

v5.1.0
======

Minor Changes
-------------

- Add ssh support for backup and restore. Also fixes CI.

v5.0.0
======

Release Summary
---------------

With this release, the minimum required version of `ansible-core` for this collection is `2.19.0`. The last version known to be compatible with `ansible-core` versions below `2.19` is v4.0.1.

Major Changes
-------------

- Bumping `dependencies` of ansible.netcommon to `>=8.1.0`, since previous versions of the dependency had compatibility issues with `ansible-core>=2.19`.

Minor Changes
-------------

- bypass space healthchecks due to compatability issues.

Bugfixes
--------

- update argspec and need too ignore random mem health status for appliance.

v4.0.1
======

Bugfixes
--------

- Adds the changelog fragments.
- Fixes the lint and sanity errors.

v4.0.0
======

Major Changes
-------------

- Consolidated the `network.backup` and `network.restore` collections into a single `network.backup` collection.
- Renamed `run` ro restore for `restore` role
- Renamed `run` to `backup` for `backup` role
- These changes are outcome of the discussion here (https://github.com/ansible/validated-content-discussion/issues/18).
- Updated the documentation to reflect these changes.

Minor Changes
-------------

- Added comprehensive Survey Configuration to gather user input and customize backup or restore processes.
- Added extensions/patterns/backup and extensions/patterns/restore directories to support backup and restore workflows.
- Added workaround to support SCP file transfer on Cisco IOS devices by bypassing libssh limitations and using system SCP CLI as a fallback.
- Created playbooks directories for each pattern, including backup/playbooks for full and differential backups, and restore/playbooks for restoring backups.
- Introduced setup.yml files to configure projects and job templates for both backup and restore patterns.
- Introduced support for user-defined custom `path` and `filename` variables for both local and remote (SCM) datastores.
- Removed redundant `operation` variable from role defaults and tasks, as it is no longer needed due to improved task variable handling.
- update backup task with timestamp as part of default file name.
- update path for survey spec

v3.0.0
======

Release Summary
---------------

With this release, the minimum required version of `ansible-core` for this collection is `2.15.0`. The last version known to be compatible with `ansible-core` versions below `2.15` is v2.0.0.

Major Changes
-------------

- Bumping `requires_ansible` to `>=2.15.0`, since previous ansible-core versions are EoL now.

Bugfixes
--------

- Fix the task to allow supported os name without fqcn.

Documentation Changes
---------------------

- Revised the instructions on when to utilize the token.
- Update README
- Updated the URL to point to validated content instead of certified content.

v2.0.0
======

Release Summary
---------------

Starting from this release, the minimum `ansible-core` version this collection requires is `2.14.0`. The last known version compatible with ansible-core<2.14 is `v1.0.0`.

Major Changes
-------------

- Update workflow and prepare release 2.0.0

v1.0.0
======

