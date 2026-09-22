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
- Keep frequently loaded AI context small.
- Model escalation and reasoning-effort escalation require explicit approval.

## PROPOSED

- Kubernetes as the application/service platform.
- GitHub Actions for CI and validation.
- Argo CD for GitOps reconciliation.
- Argo Rollouts for services where progressive delivery provides real value.
- SOPS + age for encrypted secrets.
- AdGuard Home for DNS filtering/local DNS with independent fallback.
- VLAN/network segmentation for home, servers, IoT/security, guests, and VPN.
- Dedicated firewall/router for routing, DHCP, firewall policy, VLANs, and
  WireGuard.
- Managed switch, UPS, wired APs, and future larger application server.
- Observability with Prometheus, Grafana, Loki, and Alertmanager.
- Per-service backup/restore matrix and restore-tested recovery.

## TBD

- Revalidate physical network topology, current devices, ISP/ONT/router
  constraints, Ethernet availability, and Smart View/casting behavior.
- Select Kubernetes distribution and version, if Kubernetes is approved.
- Select final secret-management implementation.
- Select final NAS model/disks/filesystem/share layout.
- Select final backup tooling, retention, encryption, restore-test cadence, and
  off-site strategy.
- Decide exact documentation language policy for future files.

## REJECTED

- Treating OpenPanel / Project-DevOps as current Home Lab state.
- Treating the 32 GB RAM / 1 TB NVMe tower as purchased or deployed.
- Creating implementation directories before there is implementation work.
- Making Kubernetes responsible for critical home routing, firewall, DHCP, or
  recovery access by default.
- Increasing model cost or reasoning effort without explicit approval.
