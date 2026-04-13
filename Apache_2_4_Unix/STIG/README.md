# Apache 2.4 STIG Policies

**Benchmarks:**
- DISA Apache Server 2.4 UNIX Server STIG V3R2 (47 controls)
- DISA Apache Server 2.4 UNIX Site STIG V2R6 (27 controls)

**Controls:** 74 total, 51 policies (22 manual, 1 low-priority skip)
**Platform:** `apache`

## Required Contracts

| Contract | Library Path | Description |
|----------|-------------|-------------|
| `apache_module` | `Apache/apache_module/` | Module presence via `httpd -M` |
| `file_content` | `RHEL9/file_system/` | httpd.conf directive checks |
| `file_metadata` | `RHEL9/file_system/` | Config/log file permissions |

## Policy Breakdown by Contract

| Contract | Policy Count |
|----------|-------------|
| `file_content` (config directives) | 25 |
| Combined (`apache_module` + `file_content`) | 17 |
| `apache_module` (module presence) | 5 |
| `file_metadata` (permissions) | 4 |

## Customization via VARs

| VAR | Default | Override For |
|-----|---------|-------------|
| `config_path` | `/etc/httpd/conf/httpd.conf` | Main Apache config file path |

## Not Covered (23 controls)

22 controls require manual review (ISSO interviews, SIEM configuration,
disaster recovery documentation, PPSM port approvals, account management).
1 low-priority control skipped (PPSM review).

## Agent Requirements

- `httpd` must be in PATH (or at `/usr/sbin/httpd`)
- Apache must be installed (httpd package)
- Filesystem access to `/etc/httpd/` config files and `/var/log/httpd/` logs
