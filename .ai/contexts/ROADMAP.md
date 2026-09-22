# Roadmap

Mark a phase complete only when its exit criteria, documentation, and evidence
are complete. Partial work remains incomplete and should be described in current
state or a task note.

- [x] Phase 0 - Context System, Baseline, and Architecture Control
  - Refactor project context into small loaded files and detailed docs.
  - Preserve original context documents under `docs/reference/original-context/`.
  - Initialize Git as the source of truth after review.
  - Establish decision tracking, prompts, and context-update rules.
  - Exit: approved context system, Git baseline, and no implementation drift.

- [ ] Phase 1 - Host and Network Inventory
  - Install Ubuntu Server 26.04.1 LTS on the Acer only after explicit task scope.
  - Inventory CPU, RAM, disk, SMART/temperature, network interfaces, routing,
    DNS, SSH, firewall, virtualization support, and Ethernet availability.
  - Revalidate old network observations.
  - Exit: factual inventory and reviewed plan.

- [ ] Phase 2 - Physical Network / Firewall Design
  - Verify ISP/ONT/router constraints and repeater/AP topology.
  - Design VLANs, firewall policy, multicast/discovery behavior, and rollback.
  - Diagnose or isolate Smart View/casting instability.
  - Exit: reviewed network design; no critical change without approval.

- [ ] Phase 3 - Host, Storage, and Backup Foundation
  - Define host baseline, storage layout, backup destinations, and restore path.
  - Exit: durable storage and basic restore path are tested.

- [ ] Phase 4 - Application Platform
  - Decide whether and how to introduce Kubernetes.
  - If approved, define distro/version, storage, ingress, policies, and
    recovery requirements before implementation.
  - Exit: sample persistent workload survives restart and recovery exercise.

- [ ] Phase 5 - GitOps and Lifecycle
  - If approved, add validation, GitOps reconciliation, version pinning, and
    deployment conventions.
  - Exit: a sample service is reproducibly deployed from Git.

- [ ] Phase 6 - Observability, Security, and Recovery
  - Add monitoring, logs, alerts, encrypted secrets, TLS, security controls,
    backup/restore matrix, and restore evidence.
  - Exit: deliberate failures are detected, diagnosed, and recovered.

- [ ] Later Phases
  - DNS/filtering, private remote access, home automation, cameras/NVR, media,
    personal services, progressive delivery, reliability exercises, incident
    management, disaster recovery, and continuous improvement.
