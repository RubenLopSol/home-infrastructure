# Home Infrastructure Target Architecture

This document is a detailed target-architecture reference. It does not describe
implemented infrastructure.

The original architecture source remains at:

```text
docs/reference/original-context/ARQUITECTURA_OBJETIVO_HOME_INFRASTRUCTURE.md
```

## Corrections Applied During Context Refactor

- The initial server baseline is the existing Acer Aspire E5-571G with Intel
  i3-4005U, 12 GB RAM, and Kingston 240 GB SSD.
- Ubuntu Server 26.04.1 LTS is the selected installation target; it is not yet
  installed.
- The previously proposed 32 GB RAM / 1 TB NVMe tower is a future option, not
  purchased or deployed current hardware.
- Dedicated firewall/router, NAS, managed switch, UPS, VLANs, Kubernetes, Argo
  CD, GitHub Actions, Argo Rollouts, SOPS + age, and AdGuard Home remain
  proposed unless later promoted in `.ai/contexts/DECISIONS.md`.
- Old network observations are pending revalidation.

## Architecture Summary

`home-infrastructure` should apply production-grade engineering practices at
home scale: source control, reproducibility, automation, least privilege,
observability, backup, restore testing, incident learning, and controlled
change.

Critical network functions remain outside Kubernetes:

- routing
- DHCP
- firewall policy
- remote recovery access

Kubernetes, if approved, is the application platform for suitable services.

## Target Direction

```text
Internet / ISP / ONT
        |
router-firewall layer
        |
home / servers / IoT-security / guest / VPN zones
        |
application platform and persistent services
        |
backups + restore-tested recovery
```

The long-term design may separate:

```text
router/firewall != application server != backup/storage system
```

That separation is a target direction, not the current implementation.

## Recovery Requirement

The recovery target is a functioning home service, not an empty redeployment.
Home-critical services must preserve configuration, credentials, paired-device
state, application data, and documented restore procedures wherever the service
supports it.

Backups are not considered validated until restore has been tested and
documented.
