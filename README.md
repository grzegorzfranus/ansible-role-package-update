# Ansible Role: Package Update

|Source|Version|CI|License|
|------|-------|--|-------|
|[![Source Code](https://img.shields.io/badge/source-github-blue.svg)](https://github.com/grzegorzfranus/ansible-role-package-update)|[![Version](https://img.shields.io/github/v/release/grzegorzfranus/ansible-role-package-update)](https://github.com/grzegorzfranus/ansible-role-package-update/releases)|[![CI](https://github.com/grzegorzfranus/ansible-role-package-update/actions/workflows/ci.yml/badge.svg)](https://github.com/grzegorzfranus/ansible-role-package-update/actions/workflows/ci.yml)|[![Repository License](https://img.shields.io/badge/license-apache2.0-brightgreen.svg)](LICENSE)|

This Ansible role manages system package updates across various Linux distributions. It provides a unified, production-ready approach to check for updates, upgrade packages, verify disk space before updates, perform controlled reboots when required, and verify system/service state post-update.

## ✨ Features

- 📦 **Unified Package Management**: Supports apt (Debian/Ubuntu) and dnf (EL/RHEL)
- 🔍 **Update Detection**: Intelligent checking for available package updates
- 🛡️ **Security Updates**: Priority support for security-only updates
- 🎯 **Selective Updates**: Granular control over which packages to update or exclude
- 💾 **Disk Space Verification**: Pre-update disk space validation across multiple paths
- 🔄 **Automated Reboots**: Safe system restarts when required with wait/delay controls
- 📊 **System Monitoring**: Pre/post-update system state tracking
- 📝 **Comprehensive Logging**: Detailed logging with remote and local storage
- 🧪 **Service Verification**: Post-update service status validation
- 🚀 **Container Testing**: Full Molecule test suite for CI/CD integration

## 🎯 Architecture

The role executes package updates using a structured execution pipeline:

```
[System Verification & Assertions]
               ↓
 [Disk Space & Capacity Checks]
               ↓
    [Pre-Update Diagnostics]
               ↓
   [Package Update Execution]
               ↓
    [Reboot Check & Action]
               ↓
  [Post-Update Service Verify]
```

## 📋 Requirements

- **Ansible**: 2.15 or higher
- **Python**: 3.9 or higher on target hosts
- **Network**: Internet/Intranet access to package repositories
- **Privileges**: sudo/root access on target hosts

### Supported operating systems
List of officially supported operating systems for this role:

| OS Family | Version | Status |
|-----------|---------|---------|
| Ubuntu | 26.04 (Resolute) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Ubuntu | 24.04 (Noble) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Ubuntu | 22.04 (Jammy) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 13 (Trixie)   | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 12 (Bookworm) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 11 (Bullseye) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| EL (RHEL, Rocky, Alma, Oracle) | 9 | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |

> **Note**: EL 8 is not supported — `python3-dnf` bindings are compiled for Python 3.6, which is incompatible with ansible-core >= 2.17. Use EL 9 or newer.

### Ansible version

Ansible >= 2.15 (fully tested with Ansible 2.15 through 2.20+)

### Python version

Python >= 3.9

### Setup module
The role uses facts gathered by Ansible on the remote host. If you disable the Setup module in your playbook, the role will not work properly.

### Root access
This role handles privilege escalation for tasks that require it. Each task explicitly defines `become: true` where needed — you do NOT need to set it globally.

## 🚀 Quick Start

### 1. Basic Package Update

```yaml
---
- name: Update system packages
  hosts: all
  roles:
    - role: grzegorzfranus.package_update
```

### 2. Advanced Configuration with Exclusions

```yaml
---
- name: Update system packages with kernel exclusion
  hosts: all
  roles:
    - role: grzegorzfranus.package_update
      vars:
        package_update_mode: "all"
        package_update_excluded_packages_list:
          - "kernel*"
          - "openssh-server*"
```

### 3. Run the playbook

```bash
ansible-playbook -i inventory package-update.yml
```

## ⚙️ Configuration

### Default Configuration

The role comes with secure and safe defaults:

```yaml
# Define which parts of the role to execute (Options: 'all', 'check')
package_update_role_action: "all"

# Update mode (Options: 'all', 'security', 'selected')
package_update_mode: "all"

# Enable or disable reboots globally
package_update_reboot_enabled: true

# Enable disk space verification before updates
package_update_disk_space_check_enabled: true
```

### Advanced Configuration

Customize for specific requirements:

```yaml
---
- name: Advanced Package Update Configuration
  hosts: all
  vars:
    package_update_mode: "selected"
    package_update_include_packages_list:
      - nginx
      - postgresql
    package_update_log_directory: "/var/log/custom-package-update"
    package_update_disk_check_paths:
      - path: "/"
        min_mb: 1000
      - path: "/boot"
        min_mb: 200
  roles:
    - role: grzegorzfranus.package_update
```

## 📊 Variables

### General Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `package_update_role_action` | Define which parts of the role to execute (Options: `all`, `check`) | `"all"` |

### Package Management Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `package_update_mode` | Update mode (Options: `all`, `security`, `selected`) | `"all"` |
| `package_update_include_packages_list` | List of packages to update (used with mode: `selected`) | `[]` |
| `package_update_excluded_packages_list` | List of packages to exclude from updates | `[]` |
| `package_update_display_upgradable_packages` | Display available packages to upgrade in output | `false` |

### Logging Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `package_update_log_upgradable_packages` | Enable logging of available packages and system changes | `true` |
| `package_update_log_directory` | Directory path where logs will be stored | `"/var/log/package-update"` |

### Disk Space Verification Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `package_update_disk_space_check_enabled` | Enable disk space verification before updates | `true` |
| `package_update_disk_check_paths` | Paths to check with per-path minimum thresholds (MB) | *See defaults/main.yml* |
| `package_update_abort_on_disk_space_error` | Abort update if disk space requirements are not met | `true` |

### Reboot Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `package_update_reboot_enabled` | Enable or disable reboots globally | `true` |
| `package_update_reboot_message` | Message to display during reboot | `"Reboot initialized by Ansible"` |
| `package_update_reboot_wait` | Wait for system to come back after reboot | `true` |
| `package_update_reboot_connect_timeout` | Connection timeout for reboot verification (seconds) | `60` |
| `package_update_reboot_wait_ctimeout` | Sleep time between connection attempts (seconds) | `5` |
| `package_update_reboot_wait_delay` | Delay before attempting connection (seconds) | `10` |
| `package_update_reboot_wait_timeout` | Maximum time to wait for connection (seconds) | `300` |
| `package_update_reboot_interval` | Pause after reboot before continuing tasks | `true` |
| `package_update_reboot_interval_seconds` | Seconds to pause after reboot | `10` |

## 📌 Role Properties

| Property | Value | Description |
|----------|-------|-------------|
| **Idempotent** | ✅ Yes | Running the role multiple times with the same parameters produces the same result. |
| **Atomic** | ❌ No | The role can be partially applied. A failure mid-execution may leave the system in a partially updated state. |
| **Check Mode** | ✅ Supported | Most tasks work in check mode. Mutating commands (package updates) are skipped. |
| **Diff Mode** | ✅ Supported | Template tasks support diff mode for change preview. |

## 📤 Role Output

This role does not set any public output facts. All logs and diagnostics are written to the directory specified by `package_update_log_directory`.

## 🔍 Verification

After execution, verify system package updates and services:

### Check Update Status

```bash
# Check for available updates (EL/RedHat)
sudo dnf check-update

# Check for available updates (Debian/Ubuntu)
sudo apt list --upgradable
```

### Verify Service Status

```bash
# Check all services are running
sudo systemctl list-units --type=service --state=running

# Check for failed services
sudo systemctl --failed
```

### Check Logs

```bash
# View update logs
sudo cat /var/log/package-update/package-update_post-system-info

# Check for any service issues
cat /var/log/package-update/package-update_faulty-services
```

## 🛡️ Security Features

- ✅ **Safe Update Process**: Pre-checks and validation before any changes
- ✅ **Controlled Reboots**: Graceful system restarts with timeout handling
- ✅ **Service Monitoring**: Automatic detection of service failures
- ✅ **Disk Space Protection**: Prevents updates that could fill the disk
- ✅ **Package Exclusions**: Protect critical packages from accidental upgrade
- ✅ **Rollback Information**: Detailed logging for troubleshooting
- ✅ **Privilege Escalation**: Per-task sudo handling (no global become required)

### Uninstall
*Not applicable.* This role acts as a lifecycle runner to perform updates rather than installing a persistent daemon.

### Roll-back Capabilities
This role does not support automatic package downgrade. Detailed package logs are kept in `package_update_log_directory` to assist manual package rolls.

## 🔒 Security considerations

- Use Ansible Vault for passing any sensitive inventory values.
- Verify log files permissions in `/var/log/package-update/` to ensure logs containing package lists are only visible to authorized system administrators.

## 🧪 Check mode behavior

- Package list updates and dry runs are performed in Check Mode, but no actual installation or reboots are executed.
- System metrics and service verification runs normally in check mode.

## 🏷️ Tags usage

- Use `--tags` to run selective parts of the role: `validate`, `check`, `prerequisites`, `disk_check`, `install`, `reboot`, `service_check`.

## 🌐 Network resilience

- Package repository actions rely on OS-level package manager timeouts. Ensure that target nodes have reliable proxy or direct internet access to update repositories.

## 🧰 Repository management

- This role does not configure repository sources directly. It manages updates using the existing active repositories on the host, running `apt-get update` or `dnf check-update` to refresh indexes before updating.

## 🔧 Troubleshooting

### Package Update Failures

```bash
# Clear package cache and retry manually
sudo apt clean       # Debian/Ubuntu
sudo dnf clean all   # EL/RedHat
```

### Disk Space Issues

```bash
# Check available disk space
df -h

# Clean package cache to reclaim space
sudo apt autoremove && sudo apt autoclean  # Debian/Ubuntu
sudo dnf autoremove                       # EL/RedHat
```

### Service Restart Problems

```bash
# Check systemd failed units
sudo systemctl --failed

# View service logs
sudo journalctl -u <service-name> --no-pager
```

## 📁 File Structure

```
ansible-role-package-update/
├── .github/
│   ├── ISSUE_TEMPLATE/                # Issue templates for bug, feature, task
│   │   ├── bug_report.yml
│   │   ├── config.yml
│   │   ├── feature_request.yml
│   │   └── task.yml
│   ├── PULL_REQUEST_TEMPLATE/         # Pull request description template
│   │   └── pull_request_template.md
│   ├── workflows/
│   │   ├── ci.yml                     # CI pipeline
│   │   └── release.yml                # Release Please + Galaxy publish
│   └── dependabot.yml                 # Dependabot configuration for GitHub Actions
├── defaults/
│   └── main.yml              # Default configuration variables
├── handlers/
│   └── main.yml              # Service restart and reload handlers
├── meta/
│   ├── main.yml              # Role metadata and Galaxy information
│   └── argument_specs.yml    # Ansible-native argument validation
├── molecule/                 # Molecule testing framework
│   └── default/              # Default test scenario
├── tasks/
│   ├── main.yml              # Main task orchestration
│   ├── assert.yml            # Variable validation and system checks
│   ├── prerequisites.yml     # System preparation
│   ├── check.yml             # Update availability detection
│   ├── disk_check.yml        # Disk space verification
│   ├── install.yml           # Package update execution
│   ├── pre_info.yml          # Pre-update state gathering
│   ├── post_info.yml         # Post-update state gathering
│   └── reboot.yml            # System reboot management
└── vars/
    ├── main.yml              # Internal role variables
    ├── debian.yml            # Debian/Ubuntu variables
    └── redhat.yml            # RedHat/EL9 variables
```

## 🏷️ Tags

| Tag | Description |
|-----|-------------|
| `always` | Tasks that always run (variable loading, validation, and core checks) |
| `validate` | Variable validation and assertion tasks |
| `check` | Update availability detection and display |
| `prerequisites` | System preparation and requirement verification |
| `disk_check` | Disk space verification tasks |
| `logging` | Information logging and documentation tasks |
| `pre_info` | Pre-update system state gathering |
| `install` | Package installation and update tasks |
| `reboot_check` | Reboot requirement detection |
| `reboot` | System restart management |
| `post_info` | Post-update system state gathering |
| `service_check` | Service status verification and comparison |

## Example Playbooks

```yaml
---
- name: Configure Package Update
  hosts: all
  become: true
  vars:
    package_update_mode: "security"
    package_update_reboot_enabled: true
    package_update_disk_space_check_enabled: true
  roles:
    - role: grzegorzfranus.package_update
```

## 🤝 Contributing

Contributions, bug reports, and feature requests are welcome!

- Fork the repository and create your branch from `main`
- Use [Conventional Commits](https://www.conventionalcommits.org/) for commit messages
- Ensure your code passes all CI checks (YAML lint, Ansible lint, Molecule tests)
- Submit a pull request describing your changes (a template is available under `.github/PULL_REQUEST_TEMPLATE/pull_request_template.md` to help structure your PR description)
- For major changes, please open an issue first to discuss what you would like to change (issue templates for bug reports, feature requests, and tasks are available under `.github/ISSUE_TEMPLATE/`)

## 📝 License

This project is licensed under the Apache-2.0 License - see the LICENSE file for details.

## 👥 Author Information

This role was created by [Grzegorz Franus](https://github.com/grzegorzfranus).
