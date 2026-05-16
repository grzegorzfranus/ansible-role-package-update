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

## 2. Global Reboot Toggle 🟡 MEDIUM

**Problem**: No option to globally disable reboots even when kernel requires it.
Enterprise environments often need to defer reboots to maintenance windows.

**Solution**: Add `package_update_reboot_enabled: true` variable.
When `false`, skip reboot entirely and log a warning that reboot is pending.

---

## 3. Execution Summary 🟡 MEDIUM

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
