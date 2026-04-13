# KSI - AWS Policies

**Framework:** KSI (Key Security Indicators) / FedRAMP 2.0
**Policies:** 26
**Platform:** `aws`

## Required Contracts

| Contract | Library Path | Description |
|----------|-------------|-------------|
| `aws_backup_vault` | `AWS/aws_backup_vault/` | Backup vault encryption and lock status |
| `aws_backup_plan` | `AWS/aws_backup_plan/` | Backup plan rules and retention |
| `aws_cloudtrail` | `AWS/aws_cloudtrail/` | CloudTrail trail configuration |
| `aws_cloudwatch_event_rule` | `AWS/aws_cloudwatch_event_rule/` | EventBridge rules |
| `aws_cloudwatch_log_group` | `AWS/aws_cloudwatch_log_group/` | Log group retention and encryption |
| `aws_cloudwatch_metric_alarm` | `AWS/aws_cloudwatch_metric_alarm/` | Metric alarm configuration |
| `aws_cloudwatch_metric_filter` | `AWS/aws_cloudwatch_metric_filter/` | Metric filter patterns |
| `aws_config_recorder` | `AWS/aws_config_recorder/` | AWS Config recording status |
| `aws_config_rule` | `AWS/aws_config_rule/` | Config rule compliance |
| `aws_ebs_volume` | `AWS/aws_ebs_volume/` | EBS volume encryption |
| `aws_ec2_instance` | `AWS/aws_ec2_instance/` | Instance hardening (IMDSv2, encryption) |
| `aws_ecr_repository` | `AWS/aws_ecr_repository/` | Container registry configuration |
| `aws_flow_log` | `AWS/aws_flow_log/` | VPC flow log configuration |
| `aws_guardduty_detector` | `AWS/aws_guardduty_detector/` | GuardDuty threat detection |
| `aws_iam_role` | `AWS/aws_iam_role/` | IAM role trust policies |
| `aws_iam_user` | `AWS/aws_iam_user/` | IAM user access key audit |
| `aws_identitystore_group` | `AWS/aws_identitystore_group/` | Identity Center groups |
| `aws_inspector2_account` | `AWS/aws_inspector2_account/` | Inspector vulnerability scanning |
| `aws_kms_key` | `AWS/aws_kms_key/` | KMS key configuration and rotation |
| `aws_macie2_account` | `AWS/aws_macie2_account/` | Macie data discovery |
| `aws_network_acl` | `AWS/aws_network_acl/` | Network ACL rules |
| `aws_route_table` | `AWS/aws_route_table/` | Route table inspection |
| `aws_s3_bucket` | `AWS/aws_s3_bucket/` | S3 bucket encryption and access |
| `aws_secretsmanager_secret` | `AWS/aws_secretsmanager_secret/` | Secrets Manager configuration |
| `aws_securityhub_account` | `AWS/aws_securityhub_account/` | Security Hub status |
| `aws_security_group` | `AWS/aws_security_group/` | Security group rules |
| `aws_ssm_maintenance_window` | `AWS/aws_ssm_maintenance_window/` | Patch maintenance windows |
| `aws_ssoadmin_permission_set` | `AWS/aws_ssoadmin_permission/` | SSO permission sets |
| `aws_vpc` | `AWS/aws_vpc/` | VPC configuration |
| `aws_vpc_endpoint` | `AWS/aws_vpc_endpoint/` | VPC endpoint configuration |

## KSI Control Families Covered

| Family | Controls | Description |
|--------|----------|-------------|
| KSI-AFR | UCM, SCN | Approved cryptographic modules, significant change notifications |
| KSI-CMT | LMC, VTD, RMV | Logging, vulnerability/threat detection, redeployment |
| KSI-CNA | MAT, RNT, ULN, IBP, DFP | Attack surface, network restriction, isolation |
| KSI-IAM | ELP, SNU, JIT, AAM, APM | Least privilege, non-user auth, just-in-time |
| KSI-MLA | LET, OSM, ALA | Logging events, SIEM, log access |
| KSI-RPL | DR, BAK | Disaster recovery, backup |
| KSI-SCR | MIT, MON, PIY | Supply chain, monitoring, SDLC security |
| KSI-SVC | ASM, VRI, EIS | Secrets management, resource integrity |

## Customization

All policies use VAR declarations for region and resource identifiers.
Override VARs per deployment without editing policy logic.

## Agent Requirements

- AWS credentials configured (access key, role, or instance profile)
- Permissions to describe/get the relevant AWS resources
- Agent runs as the AWS ESP daemon (Pattern C - AwsClient)
