# PostgreSQL 16 STIG Policies

**Benchmark:** DISA PostgreSQL 16 STIG V1R2
**Controls:** 111 total, 98 policies
**Platform:** `pg16`

## Required Contracts

| Contract | Library Path | Description |
|----------|-------------|-------------|
| `pg_config_param` | `PostgreSQL/pg_config_param/` | Runtime parameters via `psql -c "SHOW <param>"` |
| `pg_catalog_query` | `PostgreSQL/pg_catalog_query/` | System catalog queries (predefined query library) |
| `file_metadata` | `RHEL9/file_system/` | File permissions on PGDATA, log dirs, key files |
| `file_content` | `RHEL9/file_system/` | Config file checks (pg_hba.conf, pg_ident.conf) |
| `openssl_cert` | `PostgreSQL/openssl_cert/` | X.509 certificate inspection via `openssl x509` |

## Policy Breakdown by Contract

| Contract | Policy Count |
|----------|-------------|
| `pg_config_param` | 68 |
| `pg_catalog_query` | 16 |
| `file_metadata` | 12 |
| `file_content` | 4 |
| `openssl_cert` | 2 |

## Not Covered (13 controls)

- 3 controls require manual review of organizational documentation
- 10 controls require executing DDL/DML test actions and verifying audit log entries

## Authentication

PostgreSQL contracts connect via TCP (`-h 127.0.0.1 -U postgres`).

**Environment variable:** `ESP_PG_PASS`

Set this in the agent's environment before scanning. The executor dynamically
resolves it as `PGPASSWORD` on each psql call. Supports credential rotation
without agent restart.

```bash
export ESP_PG_PASS="your-postgres-password"
```

**pg_hba.conf requirement:**
```
host   all   postgres   127.0.0.1/32   scram-sha-256
```

## Agent Requirements

- `psql` must be in PATH (or at `/usr/pgsql-16/bin/psql`)
- `openssl` must be in PATH
- PostgreSQL must be running and accessible via TCP
- `ESP_PG_PASS` must be set for password auth
