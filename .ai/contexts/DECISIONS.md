# Decisions

## DECIDED

- Git will be the source of truth.
- Initial physical server is the existing Acer Aspire E5-571G with Intel
  i3-4005U, 12 GB RAM, and Kingston 240 GB SSD.
- Ubuntu Server 26.04.1 LTS is the selected installation target.
- Initial hostname: `homelab-server-01`.
- Initial administrator user: `ruben`.
- Initial installation network path: Ethernet.
- The Acer is a dedicated Home Lab server.
- The full Kingston A400 240 GB SSD is used for Ubuntu Server.
- Installer storage mode: whole disk with LVM enabled.
- Full-disk encryption/LUKS is disabled for the initial installation.
- The initial root filesystem logical volume is 100 GiB.
- Create a dedicated 80 GiB LVM logical volume for `/srv` as the initial root
  for persistent Home Lab data.
- Keep the remaining free space in `ubuntu-vg` reserved for future growth or
  new volumes when real requirements exist.
- The target storage/backup direction is a future NAS with two disks in RAID1.
- Use the existing external `BACKUP_2TB` disk as a temporary, non-destructive
  backup target until the NAS exists.
- Do not use the 64 GB USB installer/rescue device as the primary Home Lab
  backup target.
- Treat RAID1 as availability protection, not as a backup replacement.
- OpenSSH Server is installed during OS installation.
- SSH password authentication is disabled after verifying key-based SSH access.
- No orchestrator/runtime such as Docker, Podman, MicroK8s, or Kubernetes is
  installed during the OS installation.
- Keep Ubuntu network addressing on DHCP for now, with the router providing a
  DHCP reservation for the server MAC.
- Keep the Home Lab server private on the LAN; do not expose SSH or management
  services publicly in the current phase.
- Historical OpenPanel / Project-DevOps material is reference only.
- Do not treat the previously proposed 32 GB / 1 TB tower as purchased or
  deployed.
- Do not create Kubernetes, Terraform, Ansible, or other implementation
  directories until they are actually required.
- The target architecture must remain modular and horizontally expandable so
  additional physical compute nodes can be integrated cleanly in the future.
- Critical Home Lab infrastructure must not depend on a future optional AI
  compute node being powered on or present.
- Use k3s as the initial Kubernetes distribution for Phase 4.
- Start k3s as an initial single-node cluster on `homelab-server-01`, while
  preserving future multi-node expansion as a design requirement.
- Keep the initial k3s installation LAN/private and non-critical; do not place
  routing, DHCP, firewall policy, DNS dependency, or recovery access inside it.
- Disable packaged Traefik, ServiceLB, and local-storage during the initial
  k3s installation so ingress/load balancing and persistent storage can be
  designed deliberately.
- Use Rancher Local Path Provisioner as the initial single-node Kubernetes
  dynamic storage mechanism, configured to provision under `/srv/k3s/storage`.
- Treat `local-path-srv` storage as initial single-node local storage, not as
  final multi-node, NAS, or high-availability storage.
- Use `Retain` as the initial reclaim policy for `local-path-srv` so deleting a
  PVC does not automatically delete the underlying local data.
- Use Kustomize as the common Kubernetes deployment interface.
- Use Helm through Kustomize `helmCharts` when an upstream chart is mature and
  useful; use pure Kustomize for small infrastructure components where raw
  manifests plus patches provide clearer control.
- Manage Local Path Provisioner as a pure Kustomize component rather than a
  Helm chart.
- Keep frequently loaded AI context small.
- Model escalation and reasoning-effort escalation require explicit approval.

## PROPOSED

- GitHub Actions for CI and validation.
- Argo CD for GitOps reconciliation.
- Argo Rollouts for services where progressive delivery provides real value.
- SOPS + age for encrypted secrets.
- AdGuard Home for DNS filtering/local DNS with independent fallback.
- VLAN/network segmentation for home, servers, IoT/security, guests, and VPN.
- Dedicated firewall/router for routing, DHCP, firewall policy, VLANs, and
  WireGuard.
- Managed switch, UPS, wired APs, and future larger application server.
- Dedicated local AI compute node as an independent optional compute
  capability that may provide LAN AI/LLM/agent services.
- Observability with Prometheus, Grafana, Loki, and Alertmanager.
- Per-service backup/restore matrix and restore-tested recovery.

## TBD

- Revalidate physical network topology, current devices, ISP/ONT/router
  constraints, Ethernet availability, and Smart View/casting behavior.
- Select final secret-management implementation.
- Select final NAS model/disks/filesystem/share layout.
- Select final Kubernetes storage model for NAS/multi-node use.
- Select final backup tooling, retention, encryption, restore-test cadence, and
  off-site strategy.
- Decide whether future network/storage hardware should use 1 GbE, 2.5 GbE,
  10 GbE, or another approach based on measured requirements and cost.
- Select any future AI server hardware, CPU architecture/vendor, GPU
  vendor/model, VRAM/RAM/storage requirements, software stack, local models,
  virtualization or Kubernetes integration, cost, and timing.
- Decide exact future server count and physical topology.
- Decide exact documentation language policy for future files.

## REJECTED

- Treating OpenPanel / Project-DevOps as current Home Lab state.
- Treating the 32 GB RAM / 1 TB NVMe tower as purchased or deployed.
- Creating implementation directories before there is implementation work.
- Making Kubernetes responsible for critical home routing, firewall, DHCP, or
  recovery access by default.
- Increasing model cost or reasoning effort without explicit approval.
