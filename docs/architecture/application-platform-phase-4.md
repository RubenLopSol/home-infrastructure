# Application Platform Phase 4

This document starts Phase 4: deciding whether and how to introduce an
application platform on `homelab-server-01`.

It began as a design proposal and now records the initial application-platform
decision and first k3s installation evidence. GitOps, ingress, persistent
storage, production services, and monitoring remain outside the initial scope.

## Goal

Phase 4 should answer one practical question:

```text
What is the smallest useful application platform that proves deployment,
persistence, recovery, and operation on the current host without making the
home network depend on it?
```

The first platform step should be deliberately small. It should prove the
operational model before important home services depend on it.

## Current Constraints

Current host:

| Item | Value |
|---|---|
| Host | `homelab-server-01` |
| Hardware | Acer Aspire E5-571G |
| CPU | Intel Core i3-4005U |
| RAM | 12 GB |
| Disk | Kingston A400 240 GB SSD |
| OS | Ubuntu Server 26.04.1 LTS |
| Persistent data root | `/srv` on an 80 GiB ext4 LVM volume |
| Network | LAN-only, DHCP reservation at `192.168.1.181` |

Current project constraints:

- Keep routing, DHCP, firewall policy, and recovery access outside Kubernetes.
- Do not expose management services publicly.
- Do not create implementation directories before there is implementation work.
- Do not treat GitOps as a substitute for backups.
- Do not introduce important stateful services until persistence and restore
  behavior are understood.
- Keep the design portable enough to integrate future compute, storage, or AI
  nodes.

## Scope

In scope for Phase 4:

- Decide whether Kubernetes is appropriate for the first application platform
  iteration.
- Compare realistic platform options for the current hardware.
- Select a first test workload class.
- Define storage and persistence expectations for the first workload.
- Define operational checks needed before accepting the platform.
- Define the documentation and evidence required for completion.

Out of scope for the first Phase 4 decision:

- Public ingress.
- VLAN changes.
- Router/firewall replacement.
- GitOps controller installation.
- Argo Rollouts.
- Production home services such as Home Assistant, MQTT, DNS filtering, or NVR.
- Final secret-management implementation.
- NAS integration.

## Platform Options

### Option A: No Kubernetes Yet

Use simple systemd services or containers directly on the host for the first
services.

Benefits:

- Lower operational overhead.
- Fewer moving parts on the small initial host.
- Easier host-level backup and diagnosis.

Costs:

- Less practice with Kubernetes and GitOps.
- More custom per-service lifecycle handling.
- Later migration may be needed if Kubernetes remains the target.

This option is strongest if the immediate goal is useful home services with
minimal platform complexity.

### Option B: Lightweight Single-Node Kubernetes

Introduce a lightweight single-node Kubernetes distribution for a test platform
only, keeping all critical network functions outside the cluster.

Benefits:

- Exercises the intended platform direction early.
- Provides a path toward GitOps, namespaces, health checks, resource limits,
  ingress, persistent volumes, and declarative operations.
- Makes later service conventions easier to standardize.

Costs:

- Adds control-plane and container runtime overhead.
- Requires careful storage and backup design before stateful services matter.
- Requires discipline so Kubernetes does not become the accidental place for
  critical network dependencies.

This option is strongest if the immediate goal is learning and validating the
target application platform while keeping the first workload low risk.

### Option C: Defer Platform Until Future Hardware

Keep the Acer as a prepared host and postpone the application platform until a
larger server, NAS, UPS, and network hardware exist.

Benefits:

- Avoids building around temporary capacity constraints.
- Allows platform design to include future storage and network hardware from
  the start.

Costs:

- Slows practical learning and validation.
- Leaves the repository in design mode longer.
- Delays discovery of real operational constraints.

This option is strongest if hardware purchase is imminent and the current host
is not intended to run even early test workloads.

## Initial Recommendation

Accepted direction:

```text
Proceed with k3s as a constrained initial single-node Kubernetes platform.
```

The first Kubernetes iteration should be treated as a platform proof, not a
production service foundation. It should deploy one small non-critical workload,
then prove:

- basic scheduling works
- local access works from the LAN
- resource usage is acceptable
- persistent data lands under the intended storage boundary
- backup and restore behavior is understood for that workload
- platform removal or rebuild does not threaten network access

This is an initial single-node cluster, not a permanent one-node architecture.
Future workers or additional server/control-plane nodes may be introduced in
later phases, but the initial storage and availability model must not pretend
to be multi-node.

Initial k3s installation choices:

| Area | Decision |
|---|---|
| Distribution | k3s |
| Topology | Initial single-node server on `homelab-server-01` |
| Network exposure | LAN/private only |
| Critical network functions | Outside Kubernetes |
| CNI | Default k3s Flannel for the initial proof |
| CoreDNS | Keep packaged CoreDNS for cluster-internal DNS |
| Metrics server | Keep packaged metrics server for basic cluster metrics |
| Traefik | Disable initially |
| ServiceLB | Disable initially |
| Local storage provisioner | Disable initially |
| GitOps | Not installed in Phase 4 initial proof |

Traefik, ServiceLB, and local-storage are disabled initially so ingress,
load-balancing, and persistent-volume layout can be designed deliberately
instead of accepted accidentally.

## Installation Plan

Before installation:

- Host has been upgraded and rebooted into kernel `7.0.0-34-generic`.
- `/` has approximately 86G free.
- `/srv` is mounted and has approximately 75G free.
- UFW is active with default incoming deny and OpenSSH allowed.
- k3s requirements were checked against the official documentation on
  2026-09-24.

Planned UFW stance:

- Keep UFW enabled.
- Keep SSH allowed.
- Allow Kubernetes API `6443/tcp` from the LAN only.
- Allow default k3s pod CIDR `10.42.0.0/16`.
- Allow default k3s service CIDR `10.43.0.0/16`.
- Do not expose Flannel VXLAN `8472/udp` outside the local host/LAN.

Initial install command:

```bash
curl -sfL https://get.k3s.io | \
  INSTALL_K3S_CHANNEL=stable \
  INSTALL_K3S_EXEC="server \
    --node-name homelab-server-01 \
    --node-ip 192.168.1.181 \
    --write-kubeconfig-mode 644 \
    --disable traefik \
    --disable servicelb \
    --disable local-storage" \
  sh -
```

Initial validation:

```bash
sudo systemctl status k3s --no-pager
kubectl get nodes -o wide
kubectl get pods -A
kubectl get storageclass
kubectl get ingressclass
sudo ufw status verbose
```

Expected initial state:

- one ready node named `homelab-server-01`
- system pods running
- no default StorageClass from k3s local-storage
- no Traefik ingress class
- no public exposure

Rollback command if the initial installation is rejected:

```bash
sudo /usr/local/bin/k3s-uninstall.sh
```

## Installation Evidence

Initial k3s installation was completed on 2026-09-24.

Observed install result:

| Item | Value |
|---|---|
| k3s version | `v1.36.4+k3s1` |
| Service | `k3s.service` active and enabled |
| Node | `homelab-server-01` |
| Node status | `Ready` |
| Node role | `control-plane` |
| Internal IP | `192.168.1.181` |
| Runtime | `containerd://2.3.4-k3s1.36` |
| CoreDNS | running |
| metrics-server | running |
| StorageClass resources | none |
| IngressClass resources | none |
| Initial k3s data size | `467M` under `/var/lib/rancher/k3s` |

Initial resource observation:

| Scope | CPU | Memory |
|---|---:|---:|
| Node | `154m` / 3% | `703Mi` / 6% |
| CoreDNS pod | `5m` | `11Mi` |
| metrics-server pod | `15m` | `18Mi` |

UFW after installation:

| Rule | Source | Purpose |
|---|---|---|
| OpenSSH | anywhere | administration |
| `6443/tcp` | `192.168.1.0/24` | Kubernetes API from LAN |
| any | `10.42.0.0/16` | k3s pods |
| any | `10.43.0.0/16` | k3s services |

Kernel/network preparation applied before installation:

```text
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
```

The `overlay` and `br_netfilter` modules were loaded and verified.

## Stateless Workload Smoke Test

A first stateless workload smoke test was completed on 2026-09-24.

Test scope:

- namespace: `homelab-test`
- deployment: `hello-nginx`
- image: `nginx:alpine`
- replicas: `1`
- service: `hello-nginx` as `ClusterIP`
- internal test client: temporary `busybox:1.36` pod

Validated behavior:

- The pod scheduled on `homelab-server-01`.
- The pod reached `Running` with no restarts.
- The `hello-nginx` service received an internal cluster IP.
- Cluster DNS resolved `http://hello-nginx` from another pod.
- The temporary test client fetched the nginx welcome page successfully.
- Deleting the nginx pod caused the Deployment to recreate a new running pod.
- The `homelab-test` namespace was deleted after the smoke test.

This validated basic scheduling, CoreDNS, ClusterIP service routing, and
Deployment reconciliation. It did not validate persistence, ingress, backup, or
restore behavior.

## Persistent Workload Smoke Test

A first persistent workload smoke test was completed on 2026-09-24. This was a
temporary `hostPath` proof, not the final storage architecture.

Test scope:

- namespace: `homelab-persistence-test`
- pod: `persistence-smoke`
- image: `busybox:1.36`
- host path: `/srv/k3s-test/persistent-smoke`
- container mount path: `/data`
- test file: `proof.txt`

Test file content:

```text
persistent-smoke 2026-09-24T13:19:50Z
```

Validated behavior:

- The pod mounted `/srv/k3s-test/persistent-smoke` at `/data`.
- The test file was written from inside the pod.
- The same file was visible from the host.
- Deleting and recreating the pod preserved the file.
- SHA256 from host and pod matched:
  `854e5a4400247788ce16403d6875af766b4202124bae3b5ded35502c8a8ea34b`.

Backup and restore validation:

| Item | Path |
|---|---|
| Source on server | `/srv/k3s-test/persistent-smoke/proof.txt` |
| Backup on `BACKUP_2TB` | `/media/kiyana/BACKUP_2TB/HomeLab/backups/2026-09-24-k3s-persistence-smoke/persistent-smoke/proof.txt` |
| Restore test on workstation | `/tmp/homelab-restore-test-2026-09-24-k3s-persistence/persistent-smoke/proof.txt` |

The source, backup, and restored file SHA256 values matched:

```text
854e5a4400247788ce16403d6875af766b4202124bae3b5ded35502c8a8ea34b
```

The `homelab-persistence-test` namespace was deleted after validation. The
local source evidence file remains on the server under
`/srv/k3s-test/persistent-smoke/proof.txt`.

This validates a minimal persistence and restore path for the current
single-node host. It does not select a final Kubernetes storage class, dynamic
provisioner, NAS model, or multi-node storage strategy.

## Workstation kubeconfig Convention

The workstation keeps Kubernetes cluster access explicit rather than relying on
a global current context.

Current convention:

- `~/.kube/config` is intentionally a neutral/base config with no active
  context.
- `~/.kube/homelab-k3s.yaml` contains the kubeconfig for
  `homelab-server-01`.
- `~/.zshrc` defines `kh` as:

```bash
alias kh='KUBECONFIG=$HOME/.kube/homelab-k3s.yaml kubectl'
```

This keeps homelab k3s commands explicit and avoids accidental interaction
with Minikube or future clusters.

## Candidate First Workload

The first workload should be intentionally boring:

- non-critical
- LAN-only
- low CPU and memory
- no sensitive secrets
- simple persistence
- easy to verify after restore

Examples:

- a tiny static status page with no persistence
- a small test web app with a file-backed counter under `/srv`
- a disposable echo service plus a separate persistent-volume smoke test

Home Assistant, MQTT, AdGuard Home, observability, cameras/NVR, and anything
home-critical should wait until the platform proof is complete.

## Acceptance Criteria

Phase 4 can be considered complete when:

- The platform choice is documented as `DECIDED`, `DEFERRED`, or `REJECTED`.
- If Kubernetes is approved, the selected distribution and version are
  documented.
- Responsibilities outside Kubernetes are explicitly preserved.
- The first workload and persistence model are defined.
- The test workload survives restart/redeploy according to its declared state
  model.
- Restore evidence exists for any persistent test data.
- Resource usage and operational notes are documented.
- No management service is exposed publicly.

## Open Questions

- Which lightweight Kubernetes distribution is preferred for this host?
- Should the first proof use a stateless workload first, then a separate
  persistence smoke test?
- Should container runtime data stay on `/`, move under `/srv`, or use a
  dedicated future LVM volume?
- What is the minimum acceptable local ingress model for LAN-only testing?
- What validation should run before GitOps is introduced in Phase 5?
