# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [2.7.0](https://github.com/grzegorzfranus/ansible-role-package-update/compare/v2.6.1...v2.7.0) (2026-06-01)


### Features

* add support for Ubuntu 26.04 (Resolute) ([#14](https://github.com/grzegorzfranus/ansible-role-package-update/issues/14)) ([fce692c](https://github.com/grzegorzfranus/ansible-role-package-update/commit/fce692c7704be95184085bfeb651e12b4d9ea5c6))

## [2.6.1](https://github.com/grzegorzfranus/ansible-role-package-update/compare/v2.6.0...v2.6.1) (2026-05-24)


### Documentation

* **readme:** standardize package-update documentation ([#12](https://github.com/grzegorzfranus/ansible-role-package-update/issues/12)) ([3e56823](https://github.com/grzegorzfranus/ansible-role-package-update/commit/3e568238d23c6eb1e619fb5621f56e8c44b6b37b))

## [2.6.0](https://github.com/grzegorzfranus/ansible-role-package-update/compare/v2.5.0...v2.6.0) (2026-05-22)


### Features

* **ci:** add pr-title-validation, concurrency and release metadata c… ([#9](https://github.com/grzegorzfranus/ansible-role-package-update/issues/9)) ([b42b553](https://github.com/grzegorzfranus/ansible-role-package-update/commit/b42b5539ae15d362af01f98e4c12b052e532e937))

## [2.5.0](https://github.com/grzegorzfranus/ansible-role-package-update/compare/v2.4.1...v2.5.0) (2026-05-21)


### Features

* migrate to centralized CI, Release Please, and Galaxy publish ([#7](https://github.com/grzegorzfranus/ansible-role-package-update/issues/7)) ([3db58d9](https://github.com/grzegorzfranus/ansible-role-package-update/commit/3db58d98daa3c3daee2540cc5604fd4b9eb7bb89))

## [2.4.1] - 2026-05-18

### Fixed
- Upgraded `actions/checkout` from v4 to v6 (Node.js 24 compatible)
- Upgraded `actions/setup-python` from v5 to v6 (Node.js 24 compatible)

### Changed
- Standardized workflow and job naming to enterprise convention (Numbered Title Case)

## [2.4.0] - 2026-05-18

### Added
- Enterprise execution summary task at the end of the role run, displaying update status, reboot time, faulty services, and disk space

## [2.3.0] - 2026-05-18

### Added
- Official support for Debian 13 (Trixie)
- Debian 13 added to the automated Molecule CI test matrix

## [2.2.0] - 2026-05-17

### Added
- `package_update_reboot_enabled` variable to globally disable reboots
- Warning message when reboot is required but disabled
- Reboot toggle validation in `assert.yml`

### Changed
- Reboot timeout/message assertions gated by `package_update_reboot_enabled`

## [2.1.0] - 2026-05-15

### Added
- OS family validation gate in `assert.yml` — fail-fast on unsupported OS families (e.g. Alpine, SUSE)

## [2.0.0] - 2026-05-15

### Breaking Changes
- Renamed `default_reboot_*` variables to `package_update_reboot_*` for Red Hat CoP §3.1.4 compliance
- Internal registered variables renamed to `__package_update_*` double-underscore prefix convention
- `package_update_disk_check_paths` changed from list of strings to list of dicts with per-path thresholds (`path` + `min_mb`)
- Removed `package_update_min_disk_space_mb` — disk space thresholds are now per-path
- `package_update_log_file_name` removed from public API — now auto-generated internal var (`__package_update_log_file_name`)
- `package_update_check_update_command` removed from public API — now internal OS-specific var (`__package_update_check_update_command`)

### Added
- `meta/argument_specs.yml` for Ansible-native argument validation (CoP §3.1.20)
- `package_update_excluded_packages_list` to `argument_specs.yml` for formal validation
- Per-path disk space thresholds: `/boot` at 150 MB for kernel images, `/` and `/var` at 500 MB
- Documented EL 8 as unsupported (`python3-dnf` incompatible with ansible-core >= 2.17)
- Adopted `EL` (Enterprise Linux) Galaxy platform standard for RHEL-derived distributions
- Role Properties section in README (idempotency, check mode, diff mode)
- `TODO.md` with future improvement backlog

### Changed
- Upgraded ansible-lint profile from `min` to `shared` (passes `production`)
- Privilege escalation: moved `become: true` from block-level to individual tasks in `install.yml`
- Minimized task file comments to single-line headers (matching tailscale pattern)
- Added `name:` to unnamed block in `tasks/main.yml`
- Refactored `tasks/assert.yml` to runtime-only checks (basic validation delegated to `argument_specs.yml`)
- Internal vars in `vars/main.yml`, `vars/redhat.yml`, `vars/debian.yml` renamed to `__` prefix (CoP §3.1.4)
- README rewritten to Red Hat CoP §3.1.17 documentation standard
- Removed `meta-no-info` from ansible-lint skip list
- Molecule `converge.yml`: updated to actual v2.0.0 vars, relative role path (`../../..`), FQCN, `ansible.builtin.systemd` wait
- Molecule `prepare.yml`: replaced `shell` + `chmod` with idempotent `ansible.builtin.file`, added `become: true`
- Molecule `verify.yml`: replaced `ignore_errors` with `failed_when: false`, `_var_` → `__verify_` prefix, added `assert` checks
- Molecule `molecule.yml`: removed deprecated `lint:` section
- Templates updated to use `__package_update_*` internal variable names

### Fixed
- Fixed disk check false positive on `/boot` by implementing per-path thresholds
- Fixed path regex `^/.+` → `^/` in assert to accept root path `/`
- Fixed undefined variable errors in templates (`_update_check_result_`, `_pre_running_services_`, etc.)
- Added explicit `mode: "0644"` to all template tasks (`risky-file-permissions` lint compliance)
- Replaced `ignore_errors: true` with `failed_when: false` in `tasks/reboot.yml` (`ignore-errors` lint compliance)
- Fixed task key order (`when` before `block`) across `tasks/main.yml` and `tasks/install.yml`

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
