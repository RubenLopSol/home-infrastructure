# Decisions

## DECIDED

- Git will be the source of truth.
- Initial physical server is the existing Acer Aspire E5-571G with Intel
  i3-4005U, 12 GB RAM, and Kingston 240 GB SSD.
- Ubuntu Server 26.04.1 LTS is the selected installation target.
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

- Establish the first Git baseline.
- Revalidate physical network topology, current devices, ISP/ONT/router
  constraints, Ethernet availability, and Smart View/casting behavior.
- Decide whether the Acer is temporary bootstrap hardware or the longer Phase 1
  host.
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
