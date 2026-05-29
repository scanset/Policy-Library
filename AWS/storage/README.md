# CIS AWS Storage Services Benchmark v1.0.0 - ESP Policy Library

23 ESP policies implementing the CIS Amazon Web Services Storage Services
Benchmark v1.0.0. Each policy is generic - resource identifiers use named
placeholders that must be replaced with your AWS account values before scanning.

## Quick Start

1. Copy policies to a working directory
2. Replace all `PLACEHOLDER` values with your AWS resource IDs (see table below)
3. Run the ESP agent with `aws` CLI credentials configured

```bash
esp_agent assess --policy-dir ./aws-cis-storage/
```

## Placeholder Reference

Replace these values in the OBJECT blocks before scanning.

| Placeholder | Description | Example |
|---|---|---|
| `INSTANCE_ID` | EC2 instance ID | `i-0abc123def456789` |
| `ROOT_VOLUME_ID` | Root EBS volume ID | `vol-0abc123def456789` |
| `DATA_VOLUME_ID` | Data EBS volume ID | `vol-0def456789abc123` |
| `KMS_KEY_ID` | Customer-managed KMS key ID | `a1b2c3d4-e5f6-7890-abcd-ef1234567890` |
| `VM_SECURITY_GROUP_ID` | EC2 instance security group | `sg-0abc123def456789` |
| `ALB_SECURITY_GROUP_ID` | ALB security group | `sg-0def456789abc123` |
| `VPCE_SECURITY_GROUP_ID` | VPC endpoint security group | `sg-0123456789abcdef` |
| `SECURE_PORTS_SG_ID` | Security group for port checks | `sg-0fedcba987654321` |
| `VPC_ID` | VPC ID for flow log checks | `vpc-0abc123def456789` |
| `GUARDDUTY_DETECTOR_ID` | GuardDuty detector ID | `00000000000000000000000000000000` |
| `AUDIT_LOGS_BUCKET` | S3 bucket for audit logs | `myorg-audit-logs` |
| `SECURITY_FINDINGS_BUCKET` | S3 bucket for security findings | `myorg-security-findings` |
| `BACKUP_ROLE_NAME` | IAM role for AWS Backup | `myorg-backup-role` |
| `BACKUP_PREP_ROLE_NAME` | IAM role for backup preparation | `myorg-backup-prep-role` |
| `EC2_ROLE_NAME` | IAM role for EC2 instances | `myorg-ec2-role` |
| `EC2_INSTANCE_PROFILE` | EC2 instance profile name | `myorg-ec2-profile` |
| `SCANNER_IAM_USER` | IAM user running the scanner | `myorg-esp-scanner` |
| `CLOUDTRAIL_NAME` | CloudTrail trail name | `myorg-trail` |
| `CONFIG_RECORDER_NAME` | AWS Config recorder name | `myorg-config-recorder` |
| `BACKUP_VAULT_NAME` | AWS Backup vault name | `myorg-backup-vault` |
| `BACKUP_PLAN_NAME` | AWS Backup plan name | `myorg-backup-plan` |
| `DB_SECRET_ID` | Secrets Manager secret for DB creds | `myorg/db/credentials` |
| `PKI_SECRET_ID` | Secrets Manager secret for PKI key | `myorg/pki/ia-key` |
| `/CLOUDTRAIL_LOG_GROUP` | CloudWatch log group for CloudTrail | `/myorg/cloudtrail` |
| `/SECURITY_LOG_GROUP` | CloudWatch log group for security | `/myorg/security/findings` |

## Policy Inventory

### Section 1 - Identity and Access Management

| Policy | CIS Control | Description | CTN Type |
|---|---|---|---|
| `cis-aws-stor-1-4` | 1.4 | Ensure IAM policies for backup role | `aws_iam_role` |
| `cis-aws-stor-1-5` | 1.5 | Ensure IAM roles for backup | `aws_iam_role` |

### Section 2 - Storage Infrastructure Security

| Policy | CIS Control | Description | CTN Type |
|---|---|---|---|
| `cis-aws-stor-2-1` | 2.1 | Ensure EC2 instance with EBS | `aws_ec2_instance` |
| `cis-aws-stor-2-2` | 2.2 | Ensure configuring Security Groups | `aws_security_group` |
| `cis-aws-stor-2-4` | 2.4 | Ensure EBS volumes encrypted with KMS | `aws_ebs_volume` |
| `cis-aws-stor-2-6` | 2.6 | Ensure proper IAM configuration for EC2 | `aws_ec2_instance`, `aws_iam_role` |
| `cis-aws-stor-2-7` | 2.7 | Ensure creating IAM User | `aws_iam_user` |
| `cis-aws-stor-2-8` | 2.8 | Ensure threat detection for storage | `aws_guardduty_detector` |
| `cis-aws-stor-2-9` | 2.9 | Ensure AWS Config records storage resources | `aws_config_recorder` |
| `cis-aws-stor-2-10` | 2.10 | Ensure CloudTrail audits storage operations | `aws_cloudtrail` |
| `cis-aws-stor-2-11` | 2.11 | Ensure encryption key management | `aws_kms_key` |
| `cis-aws-stor-2-12` | 2.12 | Ensure monitoring with CloudWatch | `aws_cloudwatch_log_group` |
| `cis-aws-stor-2-13` | 2.13 | Ensure secrets encrypted with KMS | `aws_secretsmanager_secret` |

### Section 3 - Network Security

| Policy | CIS Control | Description | CTN Type |
|---|---|---|---|
| `cis-aws-stor-3-3` | 3.3 | Ensure VPC integration with flow logging | `aws_flow_log` |
| `cis-aws-stor-3-5` | 3.5 | Ensure using security groups for VPC | `aws_security_group` |
| `cis-aws-stor-3-6` | 3.6 | Ensure secure ports | `aws_security_group` |
| `cis-aws-stor-3-12` | 3.12 | Ensure configuring IAM for disaster recovery | `aws_iam_role` |

### Section 5 - Data Protection

| Policy | CIS Control | Description | CTN Type |
|---|---|---|---|
| `cis-aws-stor-5-1` | 5.1 | Ensure S3 audit logs bucket hardened | `aws_s3_bucket` |
| `cis-aws-stor-5-2` | 5.2 | Ensure S3 security findings bucket hardened | `aws_s3_bucket` |

### Section 6 - Disaster Recovery

| Policy | CIS Control | Description | CTN Type |
|---|---|---|---|
| `cis-aws-stor-6-1` | 6.1 | Ensure Elastic Disaster Recovery configured | `aws_backup_vault` |
| `cis-aws-stor-6-2` | 6.2 | Ensure AWS disaster recovery configuration | `aws_backup_plan` |
| `cis-aws-stor-6-4` | 6.4 | Ensure configuration of replication settings | `aws_backup_plan` |
| `cis-aws-stor-6-5` | 6.5 | Ensure proper IAM for disaster recovery | `aws_iam_role` |

## Required Contracts

The agent must have all 15 AWS CTN contracts registered. These are located in
`agent/src/contract_kit/` and registered in `agent/src/registry.rs`.

| CTN Type | Policies | AWS APIs Used |
|---|---|---|
| `aws_iam_role` | 1.4, 1.5, 2.6, 3.12, 6.5 | `iam get-role`, `iam list-attached-role-policies`, `iam list-role-policies` |
| `aws_security_group` | 2.2, 3.5, 3.6 | `ec2 describe-security-groups` |
| `aws_ec2_instance` | 2.1, 2.6 | `ec2 describe-instances`, `ec2 describe-volumes` |
| `aws_s3_bucket` | 5.1, 5.2 | `s3api get-bucket-versioning`, `get-bucket-encryption`, `get-bucket-policy-status`, `get-public-access-block`, `get-bucket-lifecycle-configuration`, `get-bucket-policy` |
| `aws_backup_plan` | 6.2, 6.4 | `backup get-backup-plan` |
| `aws_ebs_volume` | 2.4 | `ec2 describe-volumes` |
| `aws_cloudtrail` | 2.10 | `cloudtrail describe-trails`, `cloudtrail get-trail-status` |
| `aws_cloudwatch_log_group` | 2.12 | `logs describe-log-groups` |
| `aws_config_recorder` | 2.9 | `configservice describe-configuration-recorders`, `describe-configuration-recorder-status` |
| `aws_flow_log` | 3.3 | `ec2 describe-flow-logs` |
| `aws_guardduty_detector` | 2.8 | `guardduty get-detector`, `list-publishing-destinations`, `describe-publishing-destination` |
| `aws_iam_user` | 2.7 | `iam get-user`, `iam list-attached-user-policies`, `iam list-user-policies` |
| `aws_kms_key` | 2.11 | `kms describe-key`, `kms get-key-rotation-status`, `kms get-key-policy` |
| `aws_secretsmanager_secret` | 2.13 | `secretsmanager describe-secret` |
| `aws_backup_vault` | 6.1 | `backup describe-backup-vault` |

## Control Framework Mappings

Each policy maps to one or more control frameworks via the `control_mapping` META field:

- **CIS AWS Storage Services Benchmark v1.0.0** - Primary (all 23 policies)
- **NIST 800-53 Rev 5** - 20 of 23 policies (mapped via MITRE cis-cci-mappings)
- **KSI** - 20 of 23 policies (mapped via NIST 800-53 cross-reference)

## Scanner IAM Permissions

The IAM principal running the scanner requires these actions at minimum:

```json
{
    "Effect": "Allow",
    "Action": [
        "iam:GetRole",
        "iam:ListAttachedRolePolicies",
        "iam:ListRolePolicies",
        "iam:GetUser",
        "iam:ListAttachedUserPolicies",
        "iam:ListUserPolicies",
        "ec2:DescribeInstances",
        "ec2:DescribeVolumes",
        "ec2:DescribeSecurityGroups",
        "ec2:DescribeFlowLogs",
        "s3:GetBucketVersioning",
        "s3:GetEncryptionConfiguration",
        "s3:GetBucketPolicyStatus",
        "s3:GetBucketPublicAccessBlock",
        "s3:GetLifecycleConfiguration",
        "s3:GetBucketPolicy",
        "cloudtrail:DescribeTrails",
        "cloudtrail:GetTrailStatus",
        "logs:DescribeLogGroups",
        "config:DescribeConfigurationRecorders",
        "config:DescribeConfigurationRecorderStatus",
        "guardduty:GetDetector",
        "guardduty:ListPublishingDestinations",
        "guardduty:DescribePublishingDestination",
        "kms:DescribeKey",
        "kms:GetKeyRotationStatus",
        "kms:GetKeyPolicy",
        "secretsmanager:DescribeSecret",
        "backup:DescribeBackupVault",
        "backup:GetBackupPlan"
    ],
    "Resource": "*"
}
```

## Source

- Benchmark: CIS Amazon Web Services Storage Services Benchmark v1.0.0
- Extraction: `esp-workspace/01-extract/scripts/cis-extract.py`
- NIST mappings: MITRE cis-cci-mappings (github.com/mitre/cis-cci-mappings)
- Generator: `esp-workspace/skills/generate-cis-aws-storage-policies.py`
