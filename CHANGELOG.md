# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.6] - 2025-11-29

### Changed 🔄
- Removed emojis from all task names across all task files for cleaner output
- Affected files: `main.yml`, `assert.yml`, `check.yml`, `disk_check.yml`, `install.yml`, `post_info.yml`, `pre_info.yml`, `prerequisites.yml`, `reboot.yml`

## [1.0.5] - 2025-11-24

### Fixed 🔧
- Updated variable validation in `tasks/assert.yml` for Ansible 2.20 compatibility
- Replaced `regex_search()` filter with `is search()` test in reboot timeout validations
- Fixed boolean conditional requirements for:
  - `default_reboot_connect_timeout`
  - `default_reboot_wait_ctimeout`
  - `default_reboot_wait_delay`
  - `default_reboot_wait_timeout`
  - `default_reboot_interval_seconds`
- Replaced deprecated `ansible_*` variables with `ansible_facts['*']` format across all tasks
- Updated `ansible_os_family` to `ansible_facts['os_family']` in:
  - `tasks/main.yml` - reboot check conditionals
  - `tasks/install.yml` - package installation conditionals
  - `tasks/check.yml` - update check conditionals
  - `tasks/prerequisites.yml` - prerequisite installation conditionals
- Updated `ansible_pkg_mgr` to `ansible_facts['pkg_mgr']` in:
  - `tasks/install.yml` - package manager detection
  - `tasks/check.yml` - cache update conditionals
  - `tasks/prerequisites.yml` - prerequisite installation conditionals
- Updated `ansible_distribution_version` to `ansible_facts["distribution_version"]` in:
  - `tasks/install.yml` - security update logic for Debian/Ubuntu
- Updated `ansible_distribution_release` to `ansible_facts["distribution_release"]` in:
  - `tasks/install.yml` - security repository selection
  - `templates/system_info.j2` - system information reporting
- Updated all `ansible_*` fact variables to `ansible_facts['*']` format in:
  - `vars/main.yml` - log file naming (hostname, date_time)
  - `templates/system_info.j2` - system information template (hostname, kernel, distribution, date_time, cmdline)
  - `templates/pre_services_info.j2` - pre-update services report (date_time)
  - `templates/post_services_info.j2` - post-update services report (date_time)
  - `templates/packages_info.j2` - available packages report (date_time)
  - `templates/faulty_services.j2` - faulty services report (date_time)
  - `molecule/default/converge.yml` - test convergence playbook (os_family, service_mgr)
  - `molecule/default/verify.yml` - test verification playbook (os_family)

### Enhanced ✅
- Improved compatibility with Ansible 2.20 strict boolean conditional enforcement
- Eliminated all `INJECT_FACTS_AS_VARS` deprecation warnings
- Modernized fact variable usage to follow Ansible 2.20+ best practices
- Updated all templates and test files to use modern ansible_facts dictionary format

## [1.0.4] - 2025-08-10

### Fixed 🔧
- Quoted Jinja expressions in `tasks/reboot.yml` module arguments (`connect_timeout`, `reboot_timeout`, `pre_reboot_delay`, `post_reboot_delay`) to fix YAML parsing error during reboot task.

## [1.0.3] - 2025-08-10

### Changed 🔄
- Switched reboot flow to `ansible.builtin.reboot` with existing variables for safer, idempotent reboots
- Updated RedHat prerequisite from `dnf-utils` to `dnf-plugins-core`
- Added `dpkg_options: 'force-confdef,force-confold'` to apt upgrade tasks for non-interactive runs across all modes
- Hardened disk space check with `set -o pipefail` and `executable: /bin/bash`

### Fixed 🔧
- Corrected Galaxy name and examples in README to `grzegorzfranus.package_update`
- Standardized Molecule to use local role by default with env override
- Removed invalid per-platform `galaxy_tags` from `meta/main.yml`; kept top-level tags only
- Platform versions in meta now use `9` for Rocky/Oracle to align with README
- README file structure note clarified about `vars/ubuntu.yml`

## [1.0.2] - 2025-06-26

### Fixed 🔧
- Fixed variable validation logic in `tasks/assert.yml`:
  - Split `package_update_include_packages_list` validation into two separate assertions
  - Non-empty list validation now only applies when `package_update_mode` is "selected"
  - Improved error messages with clear guidance on mode requirements
- Fixed log file naming in `vars/main.yml`:
  - Removed spaces from dynamically generated log file names
  - Updated template format to prevent file system compatibility issues
  - Maintained hostname and timestamp format without spaces

### Enhanced ✅
- Added emojis to all main sections in README.md for better visual organization:
  - 📋 Requirements
  - ⚙️ Role Variables
  - 📦 Dependencies
  - 📖 Example Playbooks
  - 🎯 Main Actions (updated)
- Removed unnecessary "Architecture" section from README.md to simplify documentation
- Improved document structure and readability with consistent emoji usage
- Enhanced code quality compliance:
  - All files now pass yamllint validation without warnings
  - All files maintain ansible-lint "production" profile compliance

## [1.0.1] - 2025-06-26

### Changed ✅
- Updated GitHub Actions workflow files to match standardized pattern from ansible-role-chrony
- Renamed `ci.yml` to `test-and-validation.yml` for consistency
- Renamed `galaxy.yml` to `publish-to-galaxy.yml` for consistency
- Enhanced workflow names with status emojis 🧪📦
- Improved job and step naming with descriptive emojis (📥🐍📦🔍🚀)
- Updated workflow titles:
  - CI → 🧪 Test & Validation Pipeline
  - galaxy → 📦 Publish to Ansible Galaxy
- Enhanced job names:
  - Lint code → 🧪 Lint code
  - Molecule test → 🧪 Molecule test
  - release → 🚀 Publish role to Galaxy

### Added ✅
- Created CHANGELOG.md to track all project changes following repository standards
- Enhanced variable assertions with detailed error/success messages
- Added comprehensive validation ranges for timeout and numeric values
- Improved validation for file paths and naming conventions

### Enhanced ✅
- Updated `tasks/assert.yml` to match standardized pattern from ansible-role-chrony
- Added status emojis 🧪 to all assertion task names
- Replaced `quiet: true` with detailed `fail_msg` and `success_msg` for better debugging
- Split combined assertions into individual validation tasks for better granularity
- Added value range validations (e.g., disk space 0-1TB, timeouts 1-3600s)
- Enhanced regex validations for file names and numeric values
- Improved documentation with comprehensive header comments
- Standardized all `when` statements to use folded scalar format (`when: >`) for consistency
- Added status emojis to all task names in main.yml following chrony role pattern
- Enhanced verification and debugging tasks with descriptive emojis across all files:
  - 🔍 for check/verification/display tasks
  - 📊 for data gathering and system information tasks
  - 🧪 for test/evaluation/set_fact tasks
  - 📝 for logging tasks
  - 💾 for disk/storage operations
  - 🔄 for reboot/restart operations
  - ⏳ for wait/pause operations
  - 📦 for package operations
  - 📁 for directory operations
  - ❌ for failure/abort tasks
- Updated README.md documentation to reflect new workflow file names:
  - Badge reference updated from `ci.yml` to `test-and-validation.yml`
  - Added detailed workflow documentation section with emoji descriptions
- Enhanced README.md with comprehensive documentation sections:
  - ✨ Features section highlighting key capabilities
  - 🎯 Architecture section with workflow diagram
  - 🚀 Quick Start section with basic examples
  - 🔍 Verification section with post-deployment checks
  - 🛡️ Security Features section with best practices
  - 🔧 Troubleshooting section with common solutions
  - 📁 File Structure section with detailed directory layout
  - 🏷️ Tags section explaining all available Ansible tags
