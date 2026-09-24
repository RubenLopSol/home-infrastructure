# Initial k3s Storage

This document records the initial Kubernetes storage decision for
`homelab-server-01`.

## Decision

The initial single-node k3s storage mechanism is Rancher Local Path Provisioner
configured with pure Kustomize manifests under:

```text
kubernetes/platform/storage/local-path-provisioner/
```

The StorageClass name is:

```text
local-path-srv
```

It provisions local persistent volumes under:

```text
/srv/k3s/storage
```

## Rationale

Kustomize is the common deployment interface for Kubernetes platform
components. Helm charts should be used through Kustomize `helmCharts` when an
upstream chart is mature and useful. For Local Path Provisioner, the component
is small and benefits from explicit control, so it is managed as pure
Kustomize: raw manifests plus patches.

This mirrors the proven pattern from the previous Project-DevOps work, where
Local Path Provisioner was treated as a pure Kustomize component while larger
components such as Grafana, cert-manager, Argo Rollouts, and Velero operator
used Helm rendering through Kustomize.

## Scope

`local-path-srv` is initial local storage for the current single-node cluster.
It is not:

- final NAS storage
- multi-node storage
- high-availability storage
- a backup replacement

Services using `local-path-srv` are tied to `homelab-server-01` until a NAS or
multi-node storage design replaces this decision.

## Reclaim Policy

The initial StorageClass reclaim policy is:

```text
Retain
```

This is intentionally conservative. Deleting a PVC should not silently delete
local service data while operational habits are still being established. Manual
cleanup of released PV data is expected and must be deliberate.

## Backup Rule

Any service that stores important data on `local-path-srv` must have a backup
and restore procedure before it is considered accepted. Backups must leave the
pod/container and, for important services, must leave the host as well.

This follows feedback from the previous OpenPanel / Project-DevOps project:
generating a backup inside a pod is not enough if the backup is not copied out
to a durable recovery location.

## Smoke Test

The storage smoke test lives separately from the platform component:

```text
kubernetes/smoke-tests/storage/local-path-srv/
```

It creates:

- namespace `storage-smoke`
- PVC `local-path-srv-smoke`
- pod `local-path-srv-smoke`

The smoke test should validate PVC binding, pod mount behavior, file
persistence after pod recreation, backup to `BACKUP_2TB`, restore, and cleanup.

## Installation Evidence

`local-path-srv` was installed on 2026-09-24 from:

```text
kubernetes/platform/storage/local-path-provisioner/
```

Observed state after installation:

| Item | Value |
|---|---|
| Namespace | `local-path-storage` |
| Deployment | `local-path-provisioner` |
| Deployment status | `1/1` available |
| StorageClass | `local-path-srv` |
| Default StorageClass | yes |
| Provisioner | `rancher.io/local-path` |
| Reclaim policy | `Retain` |
| Binding mode | `WaitForFirstConsumer` |
| Data root | `/srv/k3s/storage` |

Dynamic PVC smoke test completed on 2026-09-24:

| Item | Value |
|---|---|
| Namespace | `storage-smoke` |
| PVC | `local-path-srv-smoke` |
| PVC size | `64Mi` |
| Pod | `local-path-srv-smoke` |
| Image | `busybox:1.36` |
| Test file | `proof.txt` |
| Test content | `local-path-srv smoke 2026-09-24T14:37:16Z` |
| SHA256 | `7057a562a395f4c83bdb80ee12a2fe6d137eebb91fc64e2dda48912a231d7a55` |

Validated behavior:

- PVC bound dynamically using `local-path-srv`.
- PV was created under `/srv/k3s/storage`.
- The pod mounted the PVC at `/data`.
- The test file was written from inside the pod.
- The same file and SHA256 were visible from the host.
- Deleting and recreating the pod preserved the file.
- Backup to `BACKUP_2TB` was validated at:
  `/media/kiyana/BACKUP_2TB/HomeLab/backups/2026-09-24-local-path-srv-smoke/pvc-data/`.
- Restore was validated at:
  `/tmp/homelab-restore-test-2026-09-24-local-path-srv/pvc-data/`.
- Source, staging, backup, and restore SHA256 values matched.
- The smoke-test namespace, PVC, retained PV, retained data directory, and
  temporary staging directory were deleted after validation.

Current post-test state:

- `local-path-srv` remains installed.
- `local-path-provisioner` remains running.
- No smoke-test PVs remain.
- `/srv/k3s/storage` exists and is empty after cleanup.

## Future Direction

When NAS or multi-node hardware exists, Kubernetes storage should be revisited.
Future options may include NFS/CSI-backed storage, another NAS-backed
provisioner, or a different architecture based on measured needs.
