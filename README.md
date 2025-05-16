# Ansible Role: Package Update

|Source|Version|CI|License|
|------|-------|-------|-------|
|[![Source Code](https://img.shields.io/badge/source-github-blue.svg)](https://github.com/grzegorzfranus/ansible-role-package-update)|[![Version](https://img.shields.io/github/v/release/grzegorzfranus/ansible-role-package-update)](https://github.com/grzegorzfranus/ansible-role-package-update/releases)|[![tests](https://github.com/grzegorzfranus/ansible-role-package-update/actions/workflows/ci.yml/badge.svg)](https://github.com/grzegorzfranus/ansible-role-package-update/actions)|[![Repository License](https://img.shields.io/badge/license-apache2.0-brightgreen.svg)](LICENSE)|

This Ansible role manages system package updates across various Linux distributions. It provides a unified approach to check for updates, upgrade packages, perform controlled reboots when required, and verify system state post-update.

## Main Actions

- Check for available package updates
- Install role prerequisites
- Verify disk space requirements before updates
- Upgrade system packages (all, security-only, or selected packages)
- Handle package exclusions (if specified)
- Perform controlled system reboots when required
- Collect pre and post-update system information
- Verify service status after updates
- Log all changes and system state information

## Requirements

### Supported operating systems
List of officially supported operating systems:
| OS Family | Version | Status |
|-----------|---------|---------|
| Ubuntu | 24.04 (Noble) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Ubuntu | 22.04 (Jammy) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 12 (Bookworm) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 11 (Bullseye) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Rocky Linux | 9 | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Oracle Linux | 9 | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |

### Ansible version

Ansible >= 2.15

### Python version

Python >= 3.9

### Setup module
The role uses facts gathered by Ansible on the remote host. If you disable the Setup module in your playbook, the role will not work properly.

### Root access
This role already handles privilege escalation for tasks that require it. You can invoke the role in your playbook like:
```yaml
- hosts: servers
  roles:
    - role: ansible-role-package-update
```

## Role Variables

### 1. General Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `package_update_role_action` | Define which parts of the role to execute (Options: 'all', 'check') | `"all"` |

### 2. Package Management Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `package_update_mode` | Update mode (Options: 'all', 'security', 'selected') | `"all"` |
| `package_update_include_packages_list` | List of packages to update (used with mode: 'selected') | `[]` |
| `package_update_excluded_packages_list` | List of packages to exclude from updates | `[]` |
| `package_update_display_upgradable_packages` | Display available packages to upgrade in output | `false` |

### 3. Logging Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `package_update_log_upgradable_packages` | Enable logging of available packages and system changes | `true` |
| `package_update_log_directory` | Directory path where logs will be stored | `"/var/log/package-update"` |
| `package_update_log_file_name` | Base filename for log files | `"package-update"` |

### 4. Disk Space Verification Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `package_update_disk_space_check_enabled` | Enable disk space verification before updates | `true` |
| `package_update_min_disk_space_mb` | Minimum required disk space in MB for safe update | `500` |
| `package_update_disk_check_paths` | Paths to check for disk space | `["/", "/var", "/boot"]` |
| `package_update_abort_on_disk_space_error` | Abort update if disk space requirements are not met | `true` |

### 5. Reboot Settings (Optional)

| Variable | Description | Default |
|----------|-------------|---------|
| `default_reboot_message` | Message to display during reboot | `"Reboot initialized by Ansible"` |
| `default_reboot_wait` | Wait for system to come back after reboot | `true` |
| `default_reboot_connect_timeout` | Connection timeout for reboot verification (seconds) | `60` |
| `default_reboot_wait_ctimeout` | Sleep time between connection attempts (seconds) | `5` |
| `default_reboot_wait_delay` | Delay before attempting connection (seconds) | `10` |
| `default_reboot_wait_timeout` | Maximum time to wait for connection (seconds) | `300` |
| `default_reboot_interval` | Pause after reboot before continuing tasks | `true` |
| `default_reboot_interval_seconds` | Seconds to pause after reboot | `10` |

## Dependencies

This role has no dependencies.

## Example Playbooks

### Basic usage

```yaml
- hosts: servers
  roles:
    - role: ansible-role-package-update
```

### Security updates only

```yaml
- hosts: servers
  roles:
    - role: ansible-role-package-update
      vars:
        package_update_mode: "security"
```

### Selected packages only

```yaml
- hosts: servers
  roles:
    - role: ansible-role-package-update
      vars:
        package_update_mode: "selected"
        package_update_include_packages_list:
          - nginx
          - postgresql
          - php-fpm
```

### With disk space verification customization

```yaml
- hosts: servers
  roles:
    - role: ansible-role-package-update
      vars:
        package_update_disk_space_check_enabled: true
        package_update_min_disk_space_mb: 1000
        package_update_disk_check_paths:
          - "/"
          - "/var"
        package_update_abort_on_disk_space_error: true
```

### Check-only mode (no updates performed)

```yaml
- hosts: servers
  roles:
    - role: ansible-role-package-update
      vars:
        package_update_role_action: "check"
```

## License

Apache-2.0

## Author Information

This role was created by [Grzegorz Franus](https://github.com/grzegorzfranus).

## Contributing

Contributions, bug reports, and feature requests are welcome!

- Fork the repository and create your branch from `main`.
- Make your changes with clear, descriptive commit messages.
- Ensure your code passes all Molecule and lint tests.
- Submit a pull request describing your changes and the motivation.
- For major changes, please open an issue first to discuss what you would like to change.

If you have questions or suggestions, feel free to open an issue or contact the author via GitHub.
