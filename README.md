# Ansible Role: Package Update

|Source|Version|Tests|License|
|------|-------|-------|-------|
|[![Source Code](https://img.shields.io/badge/source-github-blue.svg)](https://github.com/grzegorzfranus/ansible-role-package-update)|[![Version](https://img.shields.io/github/v/release/grzegorzfranus/ansible-role-package-update)](https://github.com/grzegorzfranus/ansible-role-package-update/releases)|[![tests](https://github.com/grzegorzfranus/ansible-role-package-update/actions/workflows/test-and-validation.yml/badge.svg)](https://github.com/grzegorzfranus/ansible-role-package-update/actions)|[![Repository License](https://img.shields.io/badge/license-apache2.0-brightgreen.svg)](LICENSE)|

This Ansible role manages system package updates across various Linux distributions. It provides a unified approach to check for updates, upgrade packages, perform controlled reboots when required, and verify system state post-update. 

## ✨ Features

- 📦 **Unified Package Management**: Supports apt (Debian/Ubuntu) and dnf (RedHat/Rocky)
- 🔍 **Update Detection**: Intelligent checking for available package updates
- 🛡️ **Security Updates**: Priority support for security-only updates
- 🎯 **Selective Updates**: Granular control over which packages to update
- 💾 **Disk Space Verification**: Pre-update disk space validation
- 🔄 **Automated Reboots**: Safe system restarts when required
- 📊 **System Monitoring**: Pre/post-update system state tracking
- 📝 **Comprehensive Logging**: Detailed logging with remote and local storage
- 🧪 **Service Verification**: Post-update service status validation
- 🚀 **CI/CD Integration**: Full Molecule test suite for automated testing

## 🎯 Main Actions

- Check for available package updates
- Install role prerequisites
- Verify disk space requirements before updates
- Upgrade system packages (all, security-only, or selected packages)
- Handle package exclusions (if specified)
- Perform controlled system reboots when required
- Collect pre and post-update system information
- Verify service status after updates
- Log all changes and system state information

## 📋 Requirements

### Supported operating systems
List of officially supported operating systems:
| OS Family | Version | Status |
|-----------|---------|---------|
| Ubuntu | 24.04 (Noble) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Ubuntu | 22.04 (Jammy) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 12 (Bookworm) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 11 (Bullseye) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Rocky Linux | 9.0 | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Oracle Linux | 9.0 | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |

### Ansible version

Ansible >= 2.15 (tested with Ansible 2.20)

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

## ⚙️ Role Variables

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
| `package_update_log_file_name` | Base filename for log files (overridden by OS vars to include hostname + timestamp) | `"<hostname>_<YYYY-MM-DD>_<HH-MM-SS>"` |

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

## 📦 Dependencies

This role has no dependencies.

## 🚀 Quick Start

### 1. Install the role

```bash
# From Ansible Galaxy
ansible-galaxy install grzegorzfranus.ansible-role-package-update

# Or clone from GitHub
git clone https://github.com/grzegorzfranus/ansible-role-package-update.git
```

### 2. Create a playbook

```yaml
---
- name: Update system packages
  hosts: all
  become: true
  roles:
    - role: ansible-role-package-update
```

### 3. Run the playbook

```bash
ansible-playbook -i inventory package-update.yml
```

> 💡 **Tip**: See [Example Playbooks](#example-playbooks) section for more advanced configurations including security-only updates, selective package updates, and custom disk space verification.

## 📖 Example Playbooks

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

### 🧪 Test & Validation Pipeline
- **Workflow**: `.github/workflows/test-and-validation.yml`
- **Name**: `🧪 Test & Validation Pipeline`
- **Purpose**: Automated testing using Molecule across multiple distributions
- **Triggers**: Push to main branch, pull requests to main/feature/release/bugfix/hotfix branches

### 📦 Galaxy Publishing
- **Workflow**: `.github/workflows/publish-to-galaxy.yml`
- **Name**: `📦 Publish to Ansible Galaxy`
- **Purpose**: Automated role publishing to Ansible Galaxy
- **Triggers**: Tagged releases (v*)

## 🔍 Verification

After deployment, verify the update process worked correctly:

### Check Update Status

```bash
# Check for available updates (RedHat)
sudo dnf check-update

# Check for available updates (Debian)
sudo apt list --upgradable

# Check system reboot requirement (RedHat)
sudo needs-restarting -r

# Check system reboot requirement (Debian)
ls -la /var/run/reboot-required
```

### Verify Service Status

```bash
# Check all services are running
sudo systemctl list-units --type=service --state=running

# Compare with pre-update service list
cat /var/log/package-update/package-update_pre-running-services

# Check for failed services
sudo systemctl --failed
```

### Check Logs

```bash
# View update logs
sudo tail -f /var/log/package-update/package-update_post-system-info

# Check for any service issues
cat /var/log/package-update/package-update_faulty-services
```

## 🛡️ Security Features

- ✅ **Safe Update Process**: Pre-checks and validation before any changes
- ✅ **Controlled Reboots**: Graceful system restarts with timeout handling
- ✅ **Service Monitoring**: Automatic detection of service failures
- ✅ **Disk Space Protection**: Prevents updates that could fill disk
- ✅ **Package Exclusions**: Ability to protect critical packages
- ✅ **Rollback Information**: Detailed logging for troubleshooting
- ✅ **Privilege Escalation**: Secure sudo handling for required tasks

### Enhanced Security Configuration

```yaml
# Protect critical packages from updates
package_update_excluded_packages_list:
  - "kernel*"
  - "nginx"
  - "postgresql*"

# Enable disk space protection
package_update_disk_space_check_enabled: true
package_update_abort_on_disk_space_error: true
```

## 🔧 Troubleshooting

### Package update failures

```bash
# Check package manager status
sudo systemctl status packagekit
sudo apt update && sudo apt upgrade --dry-run  # Debian
sudo dnf check-update  # RedHat

# Clear package cache
sudo apt clean  # Debian
sudo dnf clean all  # RedHat
```

### Disk space issues

```bash
# Check available disk space
df -h

# Find large files
sudo find /var -type f -size +100M 2>/dev/null

# Clean package cache and logs
sudo apt autoremove && sudo apt autoclean  # Debian
sudo dnf autoremove && sudo dnf clean all  # RedHat
```

### Service restart problems

```bash
# Check service status
sudo systemctl status <service-name>

# View service logs
sudo journalctl -u <service-name> --no-pager

# Restart failed services manually
sudo systemctl restart <service-name>
```

## 📁 File Structure

```
ansible-role-package-update/
├── .github/                  # GitHub Actions workflows
│   └── workflows/           # CI/CD automation
│       ├── test-and-validation.yml # 🧪 Testing and validation workflow
│       └── publish-to-galaxy.yml # 📦 Ansible Galaxy publishing workflow
├── CHANGELOG.md              # Version history and changes
├── LICENSE                   # Apache-2.0 license
├── README.md                # This documentation file
├── defaults/
│   └── main.yml             # Default configuration variables
├── handlers/
│   └── main.yml             # Service restart and reload handlers
├── meta/
│   └── main.yml             # Role metadata and Galaxy information
├── molecule/                 # Molecule testing framework
│   └── default/             # Default test scenario
│       ├── molecule.yml     # Test configuration
│       ├── converge.yml     # Role execution playbook
│       ├── prepare.yml      # Test preparation tasks
│       └── verify.yml       # Verification tests
├── tasks/
│   ├── main.yml             # Main task orchestration and flow control
│   ├── assert.yml           # Variable validation and system compatibility checks
│   ├── prerequisites.yml    # System preparation and package installation
│   ├── check.yml            # Update availability detection
│   ├── disk_check.yml       # Disk space verification
│   ├── install.yml          # Package update execution
│   ├── pre_info.yml         # Pre-update system information gathering
│   ├── post_info.yml        # Post-update system information gathering
│   └── reboot.yml           # System reboot management
├── templates/
│   ├── faulty_services.j2   # Failed services report template
│   ├── packages_info.j2     # Package information template
│   ├── post_services_info.j2 # Post-update services template
│   ├── pre_services_info.j2 # Pre-update services template
│   └── system_info.j2       # System information template
└── vars/
    ├── main.yml             # Internal role variables and constants
    ├── debian.yml           # Debian-specific variables
    ├── redhat.yml           # RedHat-specific variables
```

## 🏷️ Tags

- `always` - Tasks that always run (variable loading, validation, and core checks)
- `validate` - Variable validation and assertion tasks
- `check` - Update availability detection and display
- `prerequisites` - System preparation and requirement verification
- `disk_check` - Disk space verification tasks
- `logging` - Information logging and documentation tasks
- `pre_info` - Pre-update system state gathering
- `install` - Package installation and update tasks
- `reboot_check` - Reboot requirement detection
- `reboot` - System restart management
- `post_info` - Post-update system state gathering
- `service_check` - Service status verification and comparison

## 🤝 Contributing

Contributions, bug reports, and feature requests are welcome!

- Fork the repository and create your branch from `main`.
- Make your changes with clear, descriptive commit messages.
- Ensure your code passes all Molecule and lint tests.
- Submit a pull request describing your changes and the motivation.
- For major changes, please open an issue first to discuss what you would like to change.

If you have questions or suggestions, feel free to open an issue or contact the author via GitHub.

## 📝 License

This project is licensed under the Apache-2.0 License - see the LICENSE file for details.

## 👥 Author Information

This role was created by [Grzegorz Franus](https://github.com/grzegorzfranus).
