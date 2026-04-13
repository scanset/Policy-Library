# Endpoint State Policy - Policy Library

Official ESP policy library for DSL schema v1.0.0.

This repository contains production-ready compliance policies for DISA STIG
benchmarks, CIS benchmarks, and KSI (FedRAMP 2.0) controls. Each policy is
an `.esp` file that defines what to check, what the expected state is, and
which CTN contract executes the check.

## Related Projects

| Project | Description |
|---------|-------------|
| [ESP Core Engine](https://github.com/scanset/Endpoint-State-Policy) | Parses ESP DSL, validates policies, and executes evaluations |
| [ESP Agent SDK](https://github.com/scanset/ESP-Agent-SDK) | Agent-side execution, collectors, executors, and result packaging |
| [ESP Contract Library](https://github.com/scanset/ESP-Contract-Library) | CTN contract definitions required to execute these policies |

---

## Policy Coverage

### DISA STIG Benchmarks

| Benchmark | Directory | Policies | Controls | Coverage |
|-----------|-----------|----------|----------|----------|
| RHEL 9 STIG V2R8 | `RHEL9/STIG/` | 442 | 446 | 99% of automatable |
| PostgreSQL 16 STIG V1R2 | `PostgrSQL-16/STIG/` | 98 | 111 | 88% |
| Kubernetes STIG V2R6 | `Kubernetes/STIG/` | 86 | 92 | 93% |
| Web Server SRG V4R4 | `Web-SRG/STIG/` | 113 | 126 | 90% |
| Apache 2.4 Server + Site STIG | `Apache_2_4_Unix/STIG/` | 55 | 74 | 74% |

### CIS Benchmarks

| Benchmark | Directory | Policies | Controls | Coverage |
|-----------|-----------|----------|----------|----------|
| CIS AWS Storage Services v1.0.0 | `AWS/cis/` | 23 | 23 | 100% of automatable |

### KSI / FedRAMP 2.0

| Framework | Directory | Policies | Description |
|-----------|-----------|----------|-------------|
| KSI - AWS | `AWS/FedRAMP_20x/` | 26 | AWS infrastructure compliance |
| KSI - Azure | `Azure/az/` | 2 | Azure Entra ID / RBAC |

### Summary

| | Policies |
|--|---------|
| STIG total | 794 |
| CIS total | 23 |
| KSI total | 28 |
| **Grand total** | **845** |

All policies have been validated end-to-end against live systems with zero
parse errors and zero runtime errors.

---

## Required Contracts by Benchmark

Each benchmark requires specific CTN contracts from the
[ESP Contract Library](https://github.com/scanset/ESP-Contract-Library).
See the README in each benchmark directory for the full list.

### RHEL9 (13 contracts)

`file_content`, `file_metadata`, `rpm_package`, `mount_point`,
`sysctl_parameter`, `systemd_service`, `dconf_setting`, `grub_config`,
`filesystem_scan`, `crypto_policy`, `firewalld_rule`, `os_release`, `fips_mode`

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

### AWS KSI (30 contracts)

All `aws_*` contracts from the Contract Library (backup, cloudtrail,
cloudwatch, config, ec2, ebs, ecr, flow_log, guardduty, iam, inspector,
kms, macie, network, route, s3, secretsmanager, securityhub, security_group,
ssm, ssoadmin, vpc, vpc_endpoint, identitystore)

### Azure KSI (4 contracts)

`az_entra_application`, `az_entra_group`, `az_entra_service_principal`,
`az_role_assignment`

---

## Usage

1. Ensure your agent has the required contracts registered (see Contract Library)
2. Point the agent at a benchmark directory:
   ```bash
   esp-agent scan --policies ./RHEL9/STIG/
   ```
3. Review the assessor output for pass/fail results

### Environment Variables

| Variable | Benchmark | Purpose |
|----------|-----------|---------|
| `ESP_PG_PASS` | PostgreSQL 16 | Database password (dynamically resolved per scan) |
| `ESP_KUBECONFIG` | Kubernetes | Path to kubeconfig file |
| `AWS_ACCESS_KEY_ID` | AWS KSI, CIS AWS | AWS credentials (or use instance profile) |
| `AWS_SECRET_ACCESS_KEY` | AWS KSI, CIS AWS | AWS credentials |
| `AWS_DEFAULT_REGION` | AWS KSI, CIS AWS | AWS region |

---

## Important

Policies in this library reference CTN types that must be supported by your
agent. Before adopting any policy, ensure the required contracts from the
[ESP Contract Library](https://github.com/scanset/ESP-Contract-Library) are
registered in your scanner registry.

---

## Status

| Component | Version |
|-----------|---------|
| ESP DSL Schema | v1.0.0 |
| RHEL9 STIG | V2R8 |
| PostgreSQL 16 STIG | V1R2 |
| Kubernetes STIG | V2R6 |
| Web Server SRG | V4R4 |
| Apache 2.4 STIG | V3R2 / V2R6 |
| CIS AWS Storage | v1.0.0 |
| KSI / FedRAMP | 2.0 |
