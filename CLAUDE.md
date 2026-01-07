# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a GitOps Kubernetes homelab cluster repository. All infrastructure is declared in code and synchronized via FluxCD. The cluster ("Olympus") runs on k3s with 4 nodes (1 control-plane, 3 workers) on Ubuntu Server 24.04 LTS.

## Common Commands

```bash
# Install development tools (runs mise install)
./scripts/setup

# Bootstrap cluster (requires CLIENT_ID, CLIENT_SECRET, KUBECONFIG, CLUSTER_NAME env vars)
./scripts/bootstrap

# Flux operations
flux reconcile source git flux-system    # Trigger git sync
flux reconcile kustomization <name>      # Reconcile specific kustomization
flux get kustomizations                  # List kustomization status
flux get helmreleases -A                 # List helm releases across namespaces

# Kubernetes debugging
kubectl get events -n <namespace> --sort-by='.lastTimestamp'
kubectl logs -n <namespace> <pod>
```

## Architecture

```
clusters/olympus/     # Flux bootstrap config - root kustomizations pointing to other directories
infrastructure/
  controllers/        # Helm releases for infrastructure (cert-manager, cilium, external-secrets, harbor, etc.)
  configs/           # ClusterSecretStore, Cilium policies, ConfigMaps
apps/olympus/        # Self-hosted applications (each app in its own namespace)
monitoring/
  controllers/       # kube-prometheus-stack
  configs/          # Dashboards and monitoring config
scripts/            # bootstrap and setup scripts
tools/              # Reusable manifests for manual operations
```

**Flux dependency chain:** apps depends on infra-controllers; infra-configs depends on infra-controllers; monitoring-configs depends on monitoring-controllers.

## Application Structure Pattern

Each app in `apps/olympus/<app-name>/` follows this structure:
- `namespace.yaml` - Namespace with `harbor-pull-secret: enabled` label
- `deployment.yaml` - Container spec with security context (non-root, specific uid/gid)
- `service.yaml` - ClusterIP service
- `storage.yaml` - PersistentVolumeClaim (Synology CSI)
- `configmap.yaml` - Environment configuration
- `secrets.yaml` - ExternalSecret referencing Azure Key Vault
- `cloudflare.yaml` - Cloudflare Tunnel deployment for internet exposure
- `networkpolicy.yaml` - CiliumNetworkPolicy (allows same-namespace, monitoring, database, DNS)
- `database.yaml` - CloudNativePG cluster definition
- `barman-object-store.yaml` + `scheduled-backup.yaml` - Azure Blob backup config
- `kustomization.yaml` - Aggregates all resources

## Key Technologies

- **GitOps:** FluxCD v2
- **CNI:** Cilium (with L2 policies and load balancing)
- **Secrets:** External Secrets Operator + Azure Key Vault
- **Database:** CloudNativePG with Barman backups to Azure Blob
- **Storage:** Synology NAS via CSI
- **DNS/Certs:** external-dns (Cloudflare) + cert-manager
- **Registry:** Harbor (pull-through caching)

## Conventions

- All containers run non-root with `securityContext` (allowPrivilegeEscalation: false)
- Network policies use CiliumNetworkPolicy, not standard NetworkPolicy
- Each app gets its own namespace
- Renovate handles dependency updates (labels PRs by directory: flux, infrastructure, monitoring, apps)
- flux-system directory is excluded from Renovate

## Commit Messages

Use [Conventional Commits](https://www.conventionalcommits.org/) format:

```
<type>(<optional scope>): <description>

[optional body]

[optional footer(s)]
```

**Types:**
- `feat` - New feature or capability
- `fix` - Bug fix
- `docs` - Documentation changes only
- `style` - Formatting, whitespace (no code change)
- `refactor` - Code restructuring without behavior change
- `perf` - Performance improvements
- `test` - Adding or updating tests
- `build` - Build system or dependency changes
- `ci` - CI/CD configuration changes
- `chore` - Maintenance tasks, tooling, configs
- `revert` - Revert a previous commit

**Scope:** Optional noun describing the section of codebase affected (e.g., `harbor`, `monitoring`, `deps`)

**Breaking Changes:** Add `!` after type/scope or include `BREAKING CHANGE:` in footer

```
feat(harbor)!: change image pull secret format
```
