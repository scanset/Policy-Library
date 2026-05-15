# Endpoint State Policy - Policy Library

[![License](https://img.shields.io/badge/license-Apache--2.0-blue.svg)](LICENSE)

Official ESP policy library — production-ready compliance policies for DISA
STIG benchmarks, CIS benchmarks, KSI (FedRAMP 2.0) controls, and CMMC L1 /
NIST 800-171 controls. Each policy is an `.esp` file that defines what to
check, what the expected state is, and which CTN contract executes the check.

> **Engine compatibility:** This snapshot targets ESP engine **v2.2.3**.
> Cut a tag of this repo alongside engine releases — the policies, the
> contracts, and the engine evolve together.

## Related Projects

| Project | Description |
|---------|-------------|
| [ESP Core Engine](https://github.com/scanset/Endpoint-State-Policy) | Parses ESP DSL, validates policies, and executes evaluations |
| [ESP Agent SDK](https://github.com/scanset/ESP-Agent-SDK) | Agent-side execution, collectors, executors, and result packaging |
| [ESP Contract Library](https://github.com/scanset/Contract-Library) | CTN contract definitions required to execute these policies |

---

## Policy Coverage

### DISA STIG Benchmarks

| Benchmark | Directory | Policies | Coverage |
|-----------|-----------|----------|----------|
| RHEL 9 STIG V2R8 | `RHEL9/STIG/` | 442 | ~99% of automatable controls |
| Windows Server 2022 STIG | `Windows/Server_2022/STIG/` | 220 | Full STIG mapping |
| Web Server SRG V4R4 | `Web-SRG/STIG/` | 113 | ~90% of automatable controls |
| PostgreSQL 16 STIG V1R2 | `PostgrSQL-16/STIG/` | 98 | ~88% of automatable controls |
| Kubernetes STIG V2R6 | `Kubernetes/STIG/` | 86 | ~93% of automatable controls |
| Apache 2.4 Server + Site STIG | `Apache_2_4_Unix/STIG/` | 55 | ~74% of automatable controls |

### CIS Benchmarks

| Benchmark | Directory | Policies | Coverage |
|-----------|-----------|----------|----------|
| CIS AWS Storage Services v1.0.0 | `AWS/cis/` | 24 | Full automatable coverage |

### KSI / FedRAMP 2.0

| Scope | Directory | Policies | Description |
|-------|-----------|----------|-------------|
| KSI — AWS | `AWS/FedRAMP_20x/` | 27 | AWS infrastructure compliance |
| KSI — Azure | `Azure/ksi/` | 18 | Azure Entra ID, Defender, networking, monitoring |
| KSI — RHEL 9 | `RHEL9/KSI/` | 16 | RHEL 9 host hardening (auditd, FIPS, SELinux, SSH, sudo, kernel) |

### CMMC L1 / NIST 800-171

| Scope | Directory | Policies | Description |
|-------|-----------|----------|-------------|
| CMMC L1 — Azure | `Azure/cmmc/` | 5 | RBAC, network segmentation, Defender for Servers |
| CMMC L1 — Microsoft 365 | `M365/cmmc/` | 12 | Identity, MFA, conditional access, malware protection |

### Summary

| Category | Policies |
|---|---|
| STIG total | 1,014 |
| CIS total | 24 |
| KSI total | 61 |
| CMMC L1 total | 17 |
| **Grand total** | **~1,116** |

All policies have been validated end-to-end against live systems with zero
parse errors and zero runtime errors (on platforms where the engine wires
the required CTN types — see "Required Contracts" below).

---

## Required Contracts by Benchmark

Each benchmark requires specific CTN contracts from the
[Contract Library](https://github.com/scanset/Contract-Library). See the
README in each benchmark directory for the full per-policy list.

### RHEL 9 (13 contracts)

`file_content`, `file_metadata`, `rpm_package`, `mount_point`,
`sysctl_parameter`, `systemd_service`, `dconf_setting`, `grub_config`,
`linux_filesystem_scan`, `crypto_policy`, `firewalld_rule`, `os_release`,
`fips_mode`

### Windows Server 2022 (8 contracts)

`registry`, `windows_audit_policy`, `windows_security_policy`,
`windows_local_user`, `windows_feature`, `windows_firewall_profile`,
`windows_file_acl`, `windows_registry_acl`

Windows contracts require running the scanner on a Windows host (or
supplying a Windows-capable execution context when you build the registry).

### PostgreSQL 16 (5 contracts)

`pg_config_param`, `pg_catalog_query`, `file_metadata`, `file_content`,
`openssl_cert`

### Kubernetes (3 contracts)

`k8s_resource`, `file_metadata`, `file_content`

### Web Server SRG (4 contracts)

`tls_probe`, `http_probe`, `file_content`, `file_metadata`

### Apache 2.4 (4 contracts)

`apache_module`, `file_content`, `file_metadata`, `rpm_package`

### CIS AWS Storage (15 contracts)

`aws_iam_role`, `aws_iam_user`, `aws_ec2_instance`, `aws_ebs_volume`,
`aws_security_group`, `aws_s3_bucket`, `aws_cloudtrail`,
`aws_cloudwatch_log_group`, `aws_config_recorder`, `aws_flow_log`,
`aws_guardduty_detector`, `aws_kms_key`, `aws_secretsmanager_secret`,
`aws_backup_plan`, `aws_backup_vault`

### AWS KSI (30+ contracts)

All `aws_*` contracts from the Contract Library — backup, cloudtrail,
cloudwatch, config, ec2, ebs, ecr, flow_log, guardduty, iam, inspector,
kms, macie, network, route, s3, secretsmanager, securityhub,
security_group, ssm, ssoadmin, vpc, vpc_endpoint, identitystore.

### Azure KSI (15+ contracts)

`az_entra_application`, `az_entra_group`, `az_entra_service_principal`,
`az_role_assignment`, `az_defender_pricing`, `az_storage_account`,
`az_key_vault`, `az_log_analytics_workspace`, `az_recovery_services_vault`,
`az_nsg`, `az_virtual_network`, `az_bastion_host`, `az_security_contact`,
`az_activity_log_alert`, `az_disk_encryption_set`.

### Azure CMMC (4 contracts)

`az_defender_pricing`, `az_nsg`, `az_role_assignment_list`,
`az_virtual_network`.

### M365 CMMC (1 contract)

`m365_graph_query` — single CTN issues `GET` against arbitrary Microsoft
Graph collection paths. One contract covers every Graph resource type the
CMMC policies exercise (users, conditional access policies, sign-in logs,
device compliance, etc.).

### RHEL 9 KSI (8 contracts)

`crypto_policy`, `file_content`, `file_metadata`, `fips_mode`,
`grub_config`, `rpm_package`, `sysctl_parameter`, `systemd_service`.

---

## Usage

1. Ensure your agent has the required contracts registered (see the
   Contract Library for the drop-in `contract / collector / executor /
   command` quartets).
2. Point the agent at a benchmark or framework directory:
   ```bash
   esp_agent --output result.json ./RHEL9/STIG/
   ```
3. Review the signed `AssessorPackage` envelope (`result.json`) for
   pass/fail outcomes, findings, and the `replay_hash`.

### Environment Variables

| Variable | Used By | Purpose |
|----------|---------|---------|
| `ESP_PG_PASS` | PostgreSQL 16 STIG | Database password (dynamically resolved per scan) |
| `ESP_KUBECONFIG` | Kubernetes STIG | Path to kubeconfig file |
| `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY` / `AWS_DEFAULT_REGION` | AWS KSI, CIS AWS | AWS credentials (or use instance profile) |
| `AZURE_TENANT_ID` / `AZURE_CLIENT_ID` / `AZURE_CLIENT_SECRET` | Azure KSI, Azure CMMC, M365 CMMC | Entra app registration credentials |

---

## Important

Policies in this library reference CTN types that must be supported by
your agent. Before adopting any policy, ensure the required contracts
from the [Contract Library](https://github.com/scanset/Contract-Library)
are registered in your scanner's `CtnStrategyRegistry`. The Agent SDK's
default build ships the RHEL 9 baseline only — cloud, container, M365,
and Windows policies require vendoring additional contracts from the
Contract Library.

Two policy shapes appear in the gallery — see the ESP Language Guide's
[Policy Categories](https://github.com/scanset/ESP-Agent-SDK/blob/main/guides/ESP_Language_Guide.md#policy-categories-asset_internal-vs-asset_list)
section for the `asset_internal` vs `asset_list` distinction.

---

## Status

| Component | Version |
|-----------|---------|
| ESP engine target | v2.2.3 |
| Envelope / canonical output schema | v2.1.1 |
| ESP DSL grammar (`dsl_schema_version`) | v1.0.0 |
| RHEL 9 STIG | V2R8 |
| Windows Server 2022 STIG | V1R2 |
| PostgreSQL 16 STIG | V1R2 |
| Kubernetes STIG | V2R6 |
| Web Server SRG | V4R4 |
| Apache 2.4 STIG | V3R2 / V2R6 |
| CIS AWS Storage | v1.0.0 |
| KSI / FedRAMP | 2.0 |
| CMMC | Level 1 / NIST 800-171 |

---

## License

Apache 2.0 — see [LICENSE](LICENSE).
