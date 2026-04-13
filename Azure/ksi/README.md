# KSI - Azure Policies

**Framework:** KSI (Key Security Indicators) / FedRAMP 2.0
**Policies:** 2
**Platform:** `azure`

## Required Contracts

| Contract | Library Path | Description |
|----------|-------------|-------------|
| `az_entra_application` | `Azure/az_entra_application/` | Entra ID application registrations |
| `az_entra_group` | `Azure/az_entra_group/` | Entra security groups |
| `az_entra_service_principal` | `Azure/az_entra_service_principal/` | Service principals |
| `az_role_assignment` | `Azure/az_role_assignment/` | RBAC role assignments |

## KSI Control Families Covered

| Family | Controls | Description |
|--------|----------|-------------|
| KSI-IAM | AAM, JIT, SNU, ELP, APM | Account management, just-in-time, non-user auth, least privilege |

## Customization

Policies use VAR declarations for tenant_id, subscription_id, and
application client IDs. Override per deployment.

## Agent Requirements

- Azure CLI configured with appropriate permissions
- Agent uses AzClient (Pattern C - API-based collection)
