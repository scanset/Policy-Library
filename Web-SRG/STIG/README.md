# Web Server SRG Policies

**Benchmark:** DISA Web Server SRG V4R4
**Controls:** 126 total, 113 policies
**Platform:** `web-server`

## Required Contracts

| Contract | Library Path | Description |
|----------|-------------|-------------|
| `tls_probe` | `Network/tls_probe/` | TLS handshake probe via openssl s_client |
| `http_probe` | `Network/http_probe/` | HTTP request probe via curl |
| `file_content` | `RHEL9/file_system/` | Config file content checks |
| `file_metadata` | `RHEL9/file_system/` | File permissions and ownership |

## Policy Breakdown by Contract

| Contract | Policy Count | Category |
|----------|-------------|----------|
| `file_content` (config) | 83 | Internal config settings |
| `file_content` (logging) | 14 | Internal log configuration |
| `tls_probe` | 7 | External TLS validation |
| `file_metadata` | 6 | Internal file permissions |
| `http_probe` | 3 | External HTTP protocol checks |

## Customization via VARs

These policies use ESP VAR declarations so operators can adapt them to any
web server without editing the policy logic:

| VAR | Default | Override For |
|-----|---------|-------------|
| `web_host` | `localhost` | TLS probe target hostname |
| `web_port` | `443` | TLS probe target port |
| `web_url` | `https://localhost/` | HTTP probe target URL |
| `config_path` | `/etc/nginx/nginx.conf` | Web server main config file |

To adapt for Apache: override `config_path` to `/etc/httpd/conf/httpd.conf`
and adjust the config directive names in the STATE checks.

## Not Covered (13 controls)

12 controls require manual review of organizational documentation, procedures,
mobile code policy, fail-safe procedures, or MFA device requirements.
1 control (NSA Type 1 crypto) is a policy-level requirement.

## Server-Agnostic Design

The SRG is technology-neutral. These policies default to Nginx directive names
but the check pattern (file_content contains `<directive>`) works for any
web server by changing the VAR config_path and the expected directive names.

External checks (tls_probe, http_probe) are fully server-agnostic -- they
probe the network endpoint regardless of the underlying web server.

## Agent Requirements

- `openssl` in PATH (for tls_probe)
- `curl` in PATH (for http_probe)
- Filesystem access to web server config and log files (for file_content/file_metadata)
