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
- The initial root filesystem logical volume is 100 GiB; remaining VG space is
  intentionally left unallocated for later decision.
- OpenSSH Server is installed during OS installation.
- SSH password authentication remains temporarily enabled until key-based SSH
  access is verified.
- No orchestrator/runtime such as Docker, Podman, MicroK8s, or Kubernetes is
  installed during the OS installation.
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
- NAS or other backup/storage system.
- Managed switch, UPS, wired APs, and future larger application server.
- Observability with Prometheus, Grafana, Loki, and Alertmanager.
- Per-service backup/restore matrix and restore-tested recovery.

## TBD

- Revalidate physical network topology, current devices, ISP/ONT/router
  constraints, Ethernet availability, and Smart View/casting behavior.
- Verify SSH access from the workstation to `ruben@192.168.1.181`.
- Configure SSH key-based authentication, verify it, then disable SSH password
  authentication.
- Study/configure DHCP reservation for `homelab-server-01`.
- Decide how to allocate the remaining free space in `ubuntu-vg`.
- Select Kubernetes distribution and version, if Kubernetes is approved.
- Select final secret-management implementation.
- Select backup destinations, off-site provider, retention, and encryption
  details.
- Decide exact documentation language policy for future files.

## REJECTED

- Treating OpenPanel / Project-DevOps as current Home Lab state.
- Treating the 32 GB RAM / 1 TB NVMe tower as purchased or deployed.
- Creating implementation directories before there is implementation work.
- Making Kubernetes responsible for critical home routing, firewall, DHCP, or
  recovery access by default.
- Increasing model cost or reasoning effort without explicit approval.
