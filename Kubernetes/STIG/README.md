# Kubernetes STIG Policies

**Benchmark:** DISA Kubernetes STIG V2R6
**Controls:** 92 total, 86 policies
**Platform:** `kubernetes`

## Required Contracts

| Contract | Library Path | Description |
|----------|-------------|-------------|
| `k8s_resource` | `Kubernetes/k8s_resource/` | Kubernetes API queries via `kubectl get -o json` with RecordData |
| `file_metadata` | `RHEL9/file_system/` | File permissions on manifests, configs, PKI files |
| `file_content` | `RHEL9/file_system/` | Manifest and config content checks |

## Policy Breakdown by Contract

| Contract | Policy Count |
|----------|-------------|
| `k8s_resource` | 52 |
| `file_metadata` | 20 |
| `file_content` | 14 |

## Policy Categories

### API Server Flag Checks (24 policies)
Query the kube-apiserver pod in kube-system via `k8s_resource` with record
checks on `spec.containers.0.command` to validate flags like
`--authorization-mode`, `--tls-min-version`, `--audit-log-path`, etc.

### Etcd Flag Checks (8 policies)
Same pattern against the etcd pod for `--client-cert-auth`, `--peer-auto-tls`, etc.

### Controller Manager + Scheduler Flag Checks (6 policies)
Same pattern for `--tls-min-version`, `--bind-address`, `--root-ca-file`, etc.

### Kubelet Configuration Checks (9 policies)
Check `/var/lib/kubelet/config.yaml` via `file_content` for settings like
`readOnlyPort`, `protectKernelDefaults`, `streamingConnectionIdleTimeout`.

### File Permissions (20 policies)
Ownership and permissions on `/etc/kubernetes/manifests/`,
`/etc/kubernetes/pki/`, `/var/lib/kubelet/config.yaml`, `/var/lib/etcd/`.

### Resource Existence Checks (9 policies)
No user pods in default namespace, no dashboard deployed, PSA labels, etc.

## Not Covered (6 controls)

- 2 controls require `kubectl version` comparison (version skew)
- 2 controls require `systemd_service` on worker nodes (SSH disabled)
- 2 controls require manual review

## Authentication

kubectl uses kubeconfig for cluster auth.

**Environment variable:** `ESP_KUBECONFIG` (optional)

If set, the executor maps it to `KUBECONFIG` for the spawned kubectl process.
If not set, kubectl falls back to `~/.kube/config` (HOME is forwarded).

```bash
export ESP_KUBECONFIG="/path/to/kubeconfig"
```

For kind clusters, no additional config needed.

## Agent Requirements

- `kubectl` must be in PATH
- Valid kubeconfig with cluster access
- For file_metadata/file_content checks: agent must run ON the control plane node
- For kind clusters: file checks will fail (paths inside container); k8s_resource
  checks work correctly via the API

## Kind Cluster Notes

On kind clusters, 20 file_metadata and 14 file_content policies report findings
because the paths exist inside the container, not on the host. The 52
`k8s_resource` policies work correctly via the kubectl API. For production
kubeadm clusters where the agent runs on the control plane node, all 86
policies execute correctly.
