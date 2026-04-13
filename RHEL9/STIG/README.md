# RHEL 9 STIG Policies

**Benchmark:** DISA RHEL 9 STIG V2R8
**Controls:** 446 total, 442 policies
**Platform:** `rocky9`

## Required Contracts

The following CTN types must be registered in the agent's strategy registry
to execute these policies:

| Contract | Library Path | Description |
|----------|-------------|-------------|
| `file_content` | `RHEL9/file_system/` | File content validation (contains, not_contains, pattern_match) |
| `file_metadata` | `RHEL9/file_system/` | File permissions and ownership via `stat()` |
| `rpm_package` | `RHEL9/rpm_package/` | Package installation via `rpm -q` |
| `mount_point` | `RHEL9/mount_point/` | Mount options via `findmnt -J` |
| `sysctl_parameter` | `RHEL9/sysctl_parameter/` | Kernel parameters via `sysctl -n` |
| `systemd_service` | `RHEL9/systemd_service/` | Service state via `systemctl show` |
| `dconf_setting` | `RHEL9/dconf_setting/` | GNOME settings via `gsettings get` |
| `grub_config` | `RHEL9/grub_config/` | Bootloader configuration |
| `filesystem_scan` | `RHEL9/filesystem_scan/` | Filesystem-wide scans via `find` |
| `crypto_policy` | `RHEL9/fips/` | Crypto policy via `update-crypto-policies` |
| `firewalld_rule` | `RHEL9/firewalld_rule/` | Firewall zone config via `firewall-cmd` |
| `os_release` | `RHEL9/os_release/` | OS identity from `/etc/os-release` |
| `fips_mode` | `RHEL9/fips/` | FIPS 140 status via `fips-mode-setup` |

## Policy Breakdown by Contract

| Contract | Policy Count |
|----------|-------------|
| `file_content` | 231 |
| `file_metadata` | 57 |
| `rpm_package` | 37 |
| `mount_point` | 34 |
| `sysctl_parameter` | 33 |
| `systemd_service` | 17 |
| `dconf_setting` | 17 |
| `grub_config` | 5 |
| `filesystem_scan` | 4 |
| `crypto_policy` | 3 |
| `firewalld_rule` | 2 |
| `os_release` | 1 |
| `fips_mode` | 1 |

## Not Covered

Controls requiring manual review of organizational documentation, procedures,
or human judgment are not automatable and have no corresponding policy.

## Agent Requirements

- Agent must run as root (or with sufficient privileges for sysctl, rpm, stat, find)
- GNOME packages (gdm, dconf) must be installed for dconf_setting policies (otherwise applicable=false)
- Firewalld must be running for firewalld_rule policies
