# Contributing to the ESP Policy Library

Thank you for your interest in contributing to the **ESP Policy Library**.

This repo is a gallery of production-ready compliance policies written
in the [Endpoint State Policy (ESP)](https://github.com/scanset/Endpoint-State-Policy)
language. Each policy is a single `.esp` file that declares what to check
against a target, the expected state, and which CTN contract (from the
[Contract Library](https://github.com/scanset/Contract-Library))
executes the check.

---

## Project Philosophy

- **Policies are data** — every contribution is a `.esp` text file. No
  Rust, no scripts, no runtime code.
- **One policy per file** — keep `esp_id`, `version`, and the CTN tree
  one-per-file so consumers can pull individual checks rather than
  monolithic policy sets.
- **Map to a framework control** — every policy ties to at least one
  control via `control_mapping` (DISA-STIG, NIST-800-53, NIST-800-171,
  CIS, KSI, CMMC). Policies that don't map to a published control don't
  belong in the gallery.
- **Generic targets** — example OBJECTs use placeholder resource names
  (`example-org-*`, `your-vnet-name`, `00000000-0000-0000-0000-000000000000`)
  so consumers can copy and substitute. No real subscription IDs, tenants,
  users, or production resource names.
- **Two policy shapes** — every policy is either `asset_internal` (the
  scanner runs against the target; OBJECTs are sub-items inside it) or
  `asset_list` (the scanner enumerates external resources; each OBJECT is
  its own asset). See the [Policy Categories](https://github.com/scanset/ESP-Agent-SDK/blob/main/guides/ESP_Language_Guide.md#policy-categories-asset_internal-vs-asset_list)
  section of the language guide.

---

## Ways to Contribute

### 1. Bug Reports

If a policy is incorrect (wrong control mapping, wrong CTN type for the
check, wrong expected state, wrong field path):

- Open a GitHub Issue
- Include: policy path (e.g., `RHEL9/STIG/r9-rhel-09-211010-os-release.esp`),
  what's wrong, what the corrected behavior should be, citation to the
  upstream framework (STIG ID, CIS control number, etc.) where relevant.

### 2. New Policies

The most common contribution: adding a new `.esp` policy under the
appropriate platform/framework folder.

- **Existing framework + platform**: drop a new `.esp` in the matching
  folder (e.g., `AWS/cis/`).
- **New framework on existing platform**: create a new sub-folder under
  the platform (e.g., `Azure/iso27001/`).
- **New platform**: add a top-level platform folder and at least one
  policy inside it. Discuss in an issue first so the platform scope is
  clear.

### 3. Policy Improvements

Tightening an existing policy is welcome:

- Adding missing `control_mapping` entries
- Adding missing META fields (`category`, `target_asset_type`,
  `component_id`, `inherited_from`)
- Refining STATE constraints to catch more failure modes
- Replacing a brittle field path with a more reliable one

### 4. README + Inventory Hygiene

The coverage tables in [README.md](README.md) list the count of policies
per benchmark. When you add or remove a policy, update the relevant
table count.

---

## Repository Structure

```
Policy-Library/
├── README.md
├── LICENSE
├── CONTRIBUTING.md
├── Apache_2_4_Unix/STIG/        # Apache 2.4 Server + Site STIG
├── AWS/
│   ├── cis/                     # CIS AWS Storage Services
│   └── FedRAMP_20x/             # KSI AWS infrastructure
├── Azure/
│   ├── cmmc/                    # CMMC L1 / NIST 800-171 Azure
│   └── ksi/                     # KSI Azure infrastructure
├── Kubernetes/STIG/             # Kubernetes STIG
├── M365/cmmc/                   # CMMC L1 / NIST 800-171 Microsoft 365
├── PostgrSQL-16/STIG/           # PostgreSQL 16 STIG
├── RHEL9/
│   ├── STIG/                    # RHEL 9 STIG
│   └── KSI/                     # KSI RHEL 9 host hardening
├── Web-SRG/STIG/                # Web Server SRG
└── Windows/Server_2022/STIG/    # Windows Server 2022 STIG
```

The convention is `<Platform>/<framework>/<policy>.esp`. Lowercase
framework directories for ksi / cmmc / cis; uppercase for STIG (matches
existing convention).

---

## What "Good" Looks Like

Before opening a PR, verify your policy against this checklist.

### META block

- [ ] `esp_id` is unique across the gallery and follows the existing
      naming convention (e.g., `r9-rhel-09-211010-os-release-001`,
      `cmmc-l1-3-14-2-az-defender-plan2-001`)
- [ ] `version`, `dsl_schema_version`, `platform`, `criticality`,
      `control_mapping`, `title` — all required META fields present
- [ ] `category` — set to either `asset_internal` or `asset_list` for AI
      workflow routing
- [ ] `target_asset_type` — set when `category` is `asset_list` (e.g.,
      `Microsoft.Security/pricings`, `aws.s3.bucket`)
- [ ] `control_mapping` uses the canonical prefix for the framework:
      `DISA-STIG:<id>`, `NIST-800-53:<control>`, `NIST-800-171:<control>`,
      `CIS:<id>`, `KSI:<id>`, comma-separated when a policy covers more
      than one
- [ ] `criticality` is one of: `critical`, `high`, `medium`, `low`, `info`
- [ ] `description` and `tags` set to help discoverability

### DEF block

- [ ] Uses CTN types that exist in the [Contract Library](https://github.com/scanset/Contract-Library)
      — check the contract spec doc to confirm field names and allowed
      operations
- [ ] STATE field names match the contract's declared `StateFieldSpec`
- [ ] OBJECT field names match the contract's declared `ObjectFieldSpec`
- [ ] For `asset_list` policies, OBJECTs are wrapped in a `SET ... SET_END`
      and the CTN uses `SET_REF` rather than `OBJECT_REF`
- [ ] For `asset_internal` policies, OBJECTs reference items *inside* the
      target host (file paths, kernel parameters, services), not the
      target host itself

### Generic placeholders

- [ ] No real subscription IDs, tenant IDs, account numbers — use
      `00000000-0000-0000-0000-000000000000`
- [ ] No real resource names — use `example-org-*`, `your-vnet-name`,
      etc.
- [ ] Inline comments tell consumers to replace placeholders before
      deploying (e.g., `# Replace the OBJECT below with your VNet`)

### Parse-ability

- [ ] The policy parses against the ESP engine version listed in
      [README.md](README.md)
- [ ] If you have the Agent SDK installed, run `esp_agent <yourfile>.esp`
      and confirm it exits without compile errors. If you can't execute
      the CTN type (e.g., you don't have the cloud credentials), exit
      with a "could not run" but not a "could not parse."

---

## Pull Request Guidelines

When opening a PR:

- **Cite the upstream control** — link to the STIG ID, CIS benchmark
  section, or KSI control text the policy validates against
- **Describe what the policy checks and why** — one paragraph in the PR
  description
- **Confirm the policy parses** — include the output of
  `esp_agent --output /tmp/result.json <yourfile>.esp` (or a note that
  parse-only validation passed) in the PR body
- **Update the README count** — the affected coverage table row should
  reflect the new total
- **Keep changes focused** — one policy per PR, or one tight group of
  related policies (e.g., a complete CMMC L1 section)

---

## Engine Compatibility

Policies in this snapshot target the ESP engine version listed in
[README.md](README.md). The DSL grammar (`dsl_schema_version`) is
versioned independently of the engine release; policies authored against
v1.0.0 grammar continue to parse against newer engine releases unless
explicitly noted in the engine CHANGELOG.

When CTN types are renamed in the engine (e.g., `tcp_listener` →
`linux_tcp_listener` at engine v2.0.0), affected policies are updated in
lockstep during the refresh PR. Don't worry about cross-version
compatibility in your policy file — the engine version is a single point
in time.

---

## Signed Commits

The Policy Library is consumed in federal-compliance contexts and
requires a verifiable authorship chain. **All commits on `main` must be
signed.** The `Require signed commits` branch-protection rule is
enforced on `main`; unsigned commits will be rejected at PR merge.

### One-time setup (SSH, simpler)

If you already have an SSH key on GitHub, signing with it is the easiest
path:

```bash
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_ed25519.pub
git config --global commit.gpgsign true
git config --global tag.gpgsign true
```

Then add the same SSH key under **"Signing key"** in your GitHub
settings — this is a separate entry from the "Authentication key" slot,
even if it's the same key material:
<https://github.com/settings/keys> → "New SSH key" → set Key type to
**"Signing Key"**.

### One-time setup (GPG)

If you prefer GPG:

1. Generate a signing key if you don't have one:

   ```bash
   gpg --full-generate-key   # RSA 4096 or Ed25519 recommended
   ```

2. Export the public key and add it to your GitHub account:

   ```bash
   gpg --armor --export YOUR_KEY_ID
   # paste into https://github.com/settings/keys → "New GPG key"
   ```

3. Configure git to sign by default:

   ```bash
   git config --global user.signingkey YOUR_KEY_ID
   git config --global commit.gpgsign true
   git config --global tag.gpgsign true
   ```

### Verifying it worked

After a commit:

```bash
git log --show-signature -1
```

The commit should show `Good signature from "Your Name <email>"`. On
GitHub, the commit listing displays a green **Verified** badge.

### Release tags

Maintainers cutting a release tag (`vX.Y.Z`) — paired with the matching
ESP engine tag — must sign the tag. Tag signing is enabled by
`tag.gpgsign = true` above.

```bash
git tag -s vX.Y.Z -m "Release vX.Y.Z (paired with engine vX.Y.Z)"
git push origin vX.Y.Z
```

---

## Security Considerations

A malicious or buggy policy could:

- Cause a scanner to issue unintended commands to the target (the CTN
  command sandbox limits this, but a misconfigured policy can still
  exfiltrate state into the AssessorPackage envelope)
- Misrepresent a target's compliance posture (false-pass via a too-loose
  STATE; false-fail via a wrong field name)
- Leak target metadata in evidence fields

Policy authors should treat the AssessorPackage envelope as if it will
be reviewed by an external auditor — because in many deployments, it
will be.

If you spot a security issue in an existing policy (e.g., a STIG policy
that misses a required check, leading to a false-pass), **do not file a
public issue**. Email: **curtis@scanset.io**.

---

## Community & Conduct

The Policy Library follows a simple rule:

> Be professional, constructive, and respectful.

Strong technical opinions are welcome. Personal attacks are not.
