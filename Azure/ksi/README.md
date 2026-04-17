# KSI - Azure Policies

**Framework:** KSI (Key Security Indicators) / FedRAMP 2.0
**Policies:** 17
**Platform:** `azure`

## Policy Inventory

| File | KSI Control(s) | Criticality | Description |
|------|---------------|-------------|-------------|
| `ksi-afr-ucm-az-disk-encryption-001.esp` | KSI-AFR-UCM | critical | Disk encryption set uses customer-managed keys with auto-rotation |
| `ksi-cna-eis-az-defender-arm-001.esp` | KSI-CNA-EIS | high | Defender for Resource Manager at Standard tier |
| `ksi-cna-eis-az-defender-keyvault-001.esp` | KSI-CNA-EIS | high | Defender for Key Vault at Standard tier |
| `ksi-cna-eis-az-defender-servers-001.esp` | KSI-CNA-EIS | high | Defender for Servers at Standard P2 tier |
| `ksi-cna-eis-az-defender-storage-001.esp` | KSI-CNA-EIS | high | Defender for Storage at Standard tier |
| `ksi-cna-mat-az-bastion-secure-access-001.esp` | KSI-CNA-MAT, KSI-CNA-RNT | high | Azure Bastion for secure remote access without public IPs |
| `ksi-cna-rnt-az-nsg-baseline-001.esp` | KSI-CNA-RNT | high | NSG provisioned with security rules configured |
| `ksi-cna-uln-az-vnet-baseline-001.esp` | KSI-CNA-ULN, KSI-CNA-RNT | high | Virtual network with subnet-level segmentation |
| `ksi-dpr-dea-az-storage-tls-001.esp` | KSI-SVC-SNT | high | Storage account HTTPS-only and TLS 1.2 minimum |
| `ksi-mla-let-az-activity-alert-nsg-001.esp` | KSI-MLA-LET, KSI-CMT-LMC | high | Activity log alert for NSG write operations |
| `ksi-mla-let-az-activity-alert-policy-001.esp` | KSI-MLA-LET, KSI-CMT-LMC | high | Activity log alert for policy assignment write operations |
| `ksi-mla-let-az-security-contact-001.esp` | KSI-MLA-LET | high | Security contact with alert notifications configured |
| `ksi-mla-osm-az-log-analytics-001.esp` | KSI-MLA-OSM, KSI-MLA-RVL | high | Log Analytics workspace with 30+ day retention |
| `ksi-rpl-abo-az-rsv-backup-protection-001.esp` | KSI-RPL-ABO, KSI-RPL-ARP | high | Recovery Services vault with geo-redundancy and soft delete |
| `ksi-svc-asn-az-keyvault-protection-001.esp` | KSI-SVC-ASM | critical | Key Vault soft delete and purge protection enabled |
| `ksi_iam_aam_jit_entra_groups.esp` | KSI-IAM-AAM, KSI-IAM-JIT | high | Entra group lifecycle and JIT access |
| `ksi_iam_snu_elp_entra_applications.esp` | KSI-IAM-SNU, KSI-IAM-ELP | high | Entra application least-privilege and non-user auth |

## Required Contracts

All contracts live under `esp-workspace/contracts_output/Azure/<contract_name>/`.

| Contract | Used By Policies | Description |
|----------|------------------|-------------|
| `az_activity_log_alert` | 2 | Activity log alert rule configuration |
| `az_bastion_host` | 1 | Azure Bastion host for secure remote access |
| `az_defender_pricing` | 4 | Microsoft Defender for Cloud plan enablement |
| `az_disk_encryption_set` | 1 | Disk encryption set with customer-managed keys |
| `az_entra_application` | 1 | Entra ID application registrations |
| `az_entra_group` | 1 | Entra security groups |
| `az_entra_service_principal` | 1 | Service principals |
| `az_key_vault` | 1 | Key Vault control-plane configuration |
| `az_log_analytics_workspace` | 1 | Log Analytics workspace |
| `az_nsg` | 1 | Network security group rules |
| `az_recovery_services_vault` | 1 | Recovery Services vault backup configuration |
| `az_role_assignment` | 1 | RBAC role assignments |
| `az_security_contact` | 1 | Security Center contact and notifications |
| `az_storage_account` | 1 | Storage account control-plane configuration |
| `az_virtual_network` | 1 | Virtual network and subnet segmentation |

## KSI Control Families Covered

| Family | Controls | Description |
|--------|----------|-------------|
| KSI-AFR | UCM | Using Cryptographic Modules (FedRAMP 20x) |
| KSI-CMT | LMC | Logging Changes to the cloud service offering |
| KSI-CNA | EIS, MAT, RNT, ULN | Security posture enforcement, attack surface reduction, traffic restriction, logical networking |
| KSI-IAM | AAM, JIT, SNU, ELP, APM | Account management, just-in-time, non-user auth, least privilege, passwordless |
| KSI-MLA | LET, OSM, RVL | Event logging, SIEM operation, log review |
| KSI-RPL | ABO, ARP | Backup alignment, recovery plan alignment |
| KSI-SVC | SNT, ASM | Secure network traffic, automated secret management |

## Framework Cross-Mappings

Each policy's `control_mapping` META field includes both KSI and NIST 800-53 r5 references. CIS Azure v2.0.0 control numbers are in the tags field for rapid CIS compliance lookups.

Common cross-mapped NIST families:
- **AC** (Access Control) - IAM policies
- **AU** (Audit & Accountability) - MLA logging policies
- **CP** (Contingency Planning) - RPL backup policies
- **IR** (Incident Response) - security contact policy
- **SC** (System & Communications Protection) - encryption and network policies
- **SI** (System & Information Integrity) - Defender policies

## Customization

All production policies use placeholder resource names (`kv-example-prod`, `rg-example-eastus`, etc.) in OBJECT blocks. To apply in your environment:

1. Copy the policy to your local `esp/` directory
2. Update each OBJECT block with your actual resource names and resource group
3. Adjust assertion thresholds where noted (e.g., `retention_in_days >= 30` can be tightened to `>= 90`)
4. Run the scanner: `cargo run -- scan --esp-file esp/<policy-name>.esp`

## Agent Requirements

- Azure CLI (`az`) configured and authenticated
- Reader role on the target subscription (or scoped resource groups)
- For Defender pricing / security contact / auto-provisioning policies: subscription-level read access to `Microsoft.Security/*`
- For activity log alerts: read access to `Microsoft.Insights/activityLogAlerts`

## Policy Naming Convention

`ksi-<family>-<subfamily>-az-<resource-or-focus>-<sequence>.esp`

Examples:
- `ksi-svc-asn-az-keyvault-protection-001.esp` - KSI-SVC-ASM, Azure, keyvault protection, first version
- `ksi-mla-let-az-activity-alert-nsg-001.esp` - KSI-MLA-LET, Azure, activity alert for NSG, first version

## Related Documentation

- `esp-workspace/02-controls-db/controls-lookup.md` - KSI control statements and family reference
- `esp-workspace/contracts_output/Azure/<contract>/<contract>.md` - Field-level documentation for each contract used
- `esp-workspace/ref-docs/esp-quick-ref.md` - ESP language syntax reference
