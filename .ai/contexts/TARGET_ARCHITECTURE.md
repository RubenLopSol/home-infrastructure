# Target Architecture

This file describes intended future architecture. It is not a statement of what
is currently implemented.

## Direction

The Home Lab should evolve previous Project-DevOps practices into a real home
platform: Git as source of truth, validated changes, reproducible deployment,
observability, secure secrets, backups, restore testing, and operational
documentation.

Critical network-plane functions must stay outside Kubernetes:

- Internet routing
- firewall policy
- DHCP
- recovery access

Kubernetes is proposed as the application/service platform, not the mechanism
that keeps the home network alive.

## Proposed Physical Model

The existing Acer Aspire E5-571G is the selected initial host for the first
implementation phase, after the planned Ubuntu Server 26.04.1 LTS installation.

Future expansion may introduce separate failure and security domains:

```text
router/firewall != application server != backup/storage system
```

Future dedicated hardware such as a router/firewall, managed switch, NAS, UPS,
wired access points, or a larger application server remains proposed until
explicitly decided and acquired.

The previously proposed 32 GB RAM / 1 TB NVMe tower is a future option, not
current state.

## Proposed Platform Pattern

Proposed components and practices:

- Git-backed repository as source of truth.
- GitHub Actions for validation.
- Kubernetes for suitable services.
- Argo CD for GitOps reconciliation.
- Argo Rollouts only where progressive delivery adds operational value.
- Helm/Kustomize for pinned upstream software and local configuration.
- SOPS + age or another reviewed encrypted-secret mechanism.
- Prometheus, Grafana, Loki, and Alertmanager for observability.
- Per-service backup, retention, encryption, and restore testing.

These are not implemented yet.

## Proposed Network Direction

The proposed long-term network design includes segmented zones such as home,
servers, IoT/security, guests, and VPN. Exact VLAN IDs, subnets, firewall rules,
router/firewall model, ISP constraints, and multicast/discovery behavior remain
to be verified.

WireGuard is proposed for private remote access, preferably on the
router/firewall or another stable network-plane host rather than inside
Kubernetes.

AdGuard Home is proposed for DNS filtering and local DNS only when a recovery
or fallback path exists so DNS does not become a single catastrophic dependency.

## Recovery Principle

For stateful services, success means recovering useful state after restart,
host reboot, upgrade, restore, or total host loss. Empty replacement containers
are not sufficient.

GitOps describes desired platform state, but it does not replace backups for
application data, secrets, cryptographic identity, or external network-device
configuration.
