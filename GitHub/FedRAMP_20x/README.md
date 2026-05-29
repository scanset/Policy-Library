# KSI - GitHub SDLC Policies

**Framework:** KSI (Key Security Indicators) / FedRAMP 2.0
**Policies:** 16
**Platform:** `github`
**Category:** `asset_list`

GitHub SDLC posture policies covering source-control integrity, identity
controls, supply-chain monitoring, and webhook / deploy-key hygiene. Every
policy in this bucket carries one or more KSI mappings in
`control_mapping`; most also carry CMMC L2 and NIST 800-53 rev5 mappings,
since the same control objective recurs across the three frameworks for
SDLC platform compliance.

## Required Contracts

| Contract                   | Library Path                              | Description                                                          |
| -------------------------- | ----------------------------------------- | -------------------------------------------------------------------- |
| `github_org_settings`      | `GitHub/github_org_settings/`             | Organization-level posture (2FA, default permissions, web signoff)   |
| `github_repo_metadata`     | `GitHub/github_repo_metadata/`            | Repo-level posture (visibility, security_and_analysis flags)         |
| `github_branch_protection` | `GitHub/github_branch_protection/`        | Default-branch protection rule with 3-state semantics (200/404/403)  |
| `github_webhook`           | `GitHub/github_webhook/`                  | Per-webhook transport security (HTTPS, SSL verification)             |
| `github_deploy_key`        | `GitHub/github_deploy_key/`               | Deploy-key hygiene (read-only flag, last-used staleness)             |
| `github_workflow`          | `GitHub/github_workflow/`                 | Workflow record + OIDC binding anchor                                |

## KSI Control Families Covered

| Family   | Controls                              | Description                                                 |
| -------- | ------------------------------------- | ----------------------------------------------------------- |
| KSI-CMT  | VTD                                   | Validate-trust-by-default for SDLC changes                  |
| KSI-SVC  | VRI, SNT, ASM                         | Resource integrity, service-network traffic, secrets mgmt   |
| KSI-IAM  | ELP, SNU, TPR-adjacent                | Least privilege, named users, third-party access            |
| KSI-MLA  | LET                                   | Logging-enabled-throughout (audit integrity)                |
| KSI-RPL  | ARP                                   | Audited recovery procedures                                 |
| KSI-CNA  | RNT                                   | Runtime network traffic confidentiality / authenticity      |
| KSI-SCR  | MON, PIY                              | Supply-chain monitoring / policy integration                |
| KSI-AFR  | UCM                                   | Approved cryptographic modules                              |

## CMMC L2 Cross-Mappings

| Domain | Controls                                            |
| ------ | --------------------------------------------------- |
| IA     | IA.L2-3.5.3, IA.L2-3.5.7, IA.L2-3.5.10              |
| AC     | AC.L2-3.1.5                                         |
| CA     | CA.L2-3.12.1                                        |
| SI     | SI.L2-3.14.1                                        |
| RA     | RA.L2-3.11.2                                        |
| SC     | SC.L2-3.13.8                                        |

## NIST 800-53 rev5 Cross-Mappings

`AC-3`, `AC-6`, `AC-6(7)`, `AC-21`, `AU-9`, `AU-10`, `CA-2`, `CM-3`,
`CM-4`, `CM-5`, `CP-9`, `IA-2(1)`, `IA-2(2)`, `IA-5`, `IA-5(1)`,
`IA-5(13)`, `RA-5`, `SC-8`, `SC-8(1)`, `SC-23`, `SI-7`.

## Policy List

### Branch protection (target: SDLC::Repository, CTN: `github_branch_protection`)

| Policy | esp_id | Primary control |
|---|---|---|
| Default branch has a protection rule | `github-branch-protection-exists-001` | KSI-CMT-VTD |
| Default branch requires PR review before merge | `github-branch-protection-requires-pr-review-001` | KSI-CMT-VTD + CMMC CA.L2-3.12.1 |
| Default branch requires signed commits | `github-branch-protection-requires-signed-commits-001` | KSI-SVC-VRI + CMMC SI.L2-3.14.1 |
| Default branch blocks force-push | `github-branch-protection-blocks-force-push-001` | KSI-MLA-LET + NIST AU-9 |
| Default branch blocks deletion | `github-branch-protection-blocks-deletion-001` | KSI-MLA-LET + KSI-RPL-ARP |
| Default branch protection rule enforces admins | `github-branch-protection-enforces-admins-001` | KSI-IAM-ELP + CMMC AC.L2-3.1.5 |

### Org settings (target: SDLC::Organization, CTN: `github_org_settings`)

| Policy | esp_id | Primary control |
|---|---|---|
| Org enforces 2FA on members | `github-org-2fa-enforced-001` | KSI-IAM-SNU + CMMC IA.L2-3.5.3 |
| Org default repository permission is least-privilege | `github-org-no-default-write-permission-001` | KSI-IAM-ELP + CMMC AC.L2-3.1.5 |
| Org restricts member invitation of outside collaborators | `github-org-restricts-outside-collaborators-001` | KSI-IAM-ELP + KSI-TPR |

### Repository metadata (target: SDLC::Repository, CTN: `github_repo_metadata`)

| Policy | esp_id | Primary control |
|---|---|---|
| Repo has secret scanning enabled | `github-repo-secret-scanning-enabled-001` | KSI-SVC-ASM + CMMC IA.L2-3.5.10 |
| Repo has Dependabot security updates enabled | `github-repo-dependabot-alerts-enabled-001` | KSI-SCR-MON + CMMC RA.L2-3.11.2 |
| Repo requires web-UI commit signoff (DCO) | `github-repo-web-commit-signoff-required-001` | KSI-SVC-VRI + NIST SI-7 |

### Webhook transport (target: SDLC::Webhook, CTN: `github_webhook`)

| Policy | esp_id | Primary control |
|---|---|---|
| Webhook target URL uses HTTPS | `github-webhook-uses-https-001` | KSI-CNA-RNT + CMMC SC.L2-3.13.8 |
| Webhook SSL verification is enabled | `github-webhook-ssl-verify-enabled-001` | KSI-CNA-RNT + NIST SC-23 |

### Deploy keys (target: SDLC::DeployKey, CTN: `github_deploy_key`)

| Policy | esp_id | Primary control |
|---|---|---|
| Deploy key is read-only | `github-deploykey-read-only-001` | KSI-IAM-ELP + NIST AC-6(7) |
| Deploy key is not stale (used within 90 days) | `github-deploykey-not-stale-001` | KSI-IAM-ELP + CMMC IA.L2-3.5.10 |

## Dispatch model

Policies use `category = asset_list`. The scanner iterates the SET in-process,
invoking the named CTN once per bound asset. There is no host-side channel;
all work is HTTPS GET requests from the central Prooflayer server to
`api.github.com` (or to a configured GitHub Enterprise Server `/api/v3`
host) using the bearer token from a `github_pat` credential.

## Auth + env

All 16 policies require a `github_pat` credential. The resolver exposes
`GITHUB_TOKEN` (the PAT) and `GITHUB_BASE_URL` (default
`https://api.github.com`) to the CTN at scan time. PAT permission
requirements per CTN are documented in each contract's `.md` file under
the corresponding Contract-Library/GitHub/ directory.

## PolicySetSync binding

Each policy declares `target_asset_type` in META and an empty-bodied
template OBJECT inside its SET. PolicySetSync reads
`inventory.asset_policies` for active (policy_id, asset_id) bindings,
filters to the asset_type the policy targets, and rewrites the SET body
with one OBJECT per bound asset, each carrying the asset's concrete
metadata fields (owner / repo / branch / login / webhook_id / key_id) as
literal backtick values. The VAR declarations above the SET are
pattern-fillers used by the template OBJECT before PolicySetSync runs.
