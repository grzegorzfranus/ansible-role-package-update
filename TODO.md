# ansible-role-package-update — Future Improvements

Observations from Red Hat CoP compliance audit (2026-05-15).
To be implemented as separate branches/versions after v2.0.0 merge.

## 1. OS Family Validation Gate ✅ DONE (v2.1.0)

**Problem**: Role does not validate in `assert.yml` whether the target OS is supported.
Running on an unsupported OS (e.g. Alpine, SUSE) will produce unpredictable behavior.

**Solution**: Add OS family assertion at the beginning of `assert.yml`:

```yaml
- name: Package-Update | assert | Validate supported OS family
  ansible.builtin.assert:
    that:
      - ansible_facts['os_family'] | lower in ['redhat', 'debian']
    fail_msg: "Unsupported OS family '{{ ansible_facts['os_family'] }}'. Supported: RedHat, Debian"
    success_msg: "OS family: {{ ansible_facts['os_family'] }}"
```

---

## 2. Global Reboot Toggle ✅ DONE (v2.2.0)

**Problem**: No option to globally disable reboots even when kernel requires it.
Enterprise environments often need to defer reboots to maintenance windows.

**Solution**: Add `package_update_reboot_enabled: true` variable.
When `false`, skip reboot entirely and log a warning that reboot is pending.

---

## 3. Molecule Multi-Distro Coverage & Debian 13 ✅ DONE (v2.3.0)

**Problem**: Debian 13 (Trixie) is not included in the Molecule test matrix or documented as supported.
Additionally, the CI matrix should be audited to ensure every platform listed in `README.md` is covered.

**Current CI matrix** (`test-and-validation.yml`):
- ✅ Ubuntu 24.04 (`ubuntu2404`)
- ✅ Ubuntu 22.04 (`ubuntu2204`)
- ✅ Debian 12 (`debian12`)
- ✅ Debian 11 (`debian11`)
- ✅ Rocky Linux 9 (`rockylinux9`)
- ❌ Debian 13 (`debian13`) — **missing**

**Solution**:
1. Add `debian13` to the CI matrix in `.github/workflows/test-and-validation.yml`
2. Add Debian 13 to supported platforms in `README.md`, `meta/main.yml`, and `meta/argument_specs.yml`
3. Verify `geerlingguy/docker-debian13-ansible` image availability
4. Add `vars/debian_13.yml` if Debian 13 requires OS-specific variable overrides

---

## 4. Execution Summary 🟡 MEDIUM

**Problem**: No end-of-run summary showing what happened.
Enterprise pattern: clear summary of actions taken.

**Solution**: Add a summary task at the end of main.yml displaying:
- Number of packages updated
- Whether reboot was performed (and elapsed time)
- List of faulty services (if any)
- Disk space status per partition

---

## References

- CoP: https://redhat-cop.github.io/automation-good-practices/
- Reference role: `roles/ansible-role-tailscale`
