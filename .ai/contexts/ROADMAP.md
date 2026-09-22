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

- [x] Phase 1 - Host and Network Inventory
  - Ubuntu Server 26.04.1 LTS base installation on the Acer is complete.
  - Inventory CPU, RAM, disk, SMART/temperature, network interfaces, routing,
    DNS, SSH, firewall, virtualization support, and Ethernet availability.
  - Basic host and network inventory is complete. Detailed physical network
    revalidation continues in Phase 2.
  - Exit: factual inventory and reviewed plan.

- [ ] Phase 2 - Physical Network / Firewall Design
  - [x] Phase 2A: current network baseline with existing hardware is documented.
  - [x] DHCP reservation for `homelab-server-01` is configured and validated.
  - [x] Current limitation is documented: available hardware is the ISP/router,
    existing repeaters, and `homelab-server-01`.
  - [ ] Phase 2B is pending future hardware purchases and decisions.
  - Keep VLANs, dedicated firewall/router, managed switch, NAS, UPS, and wired
    APs as future/proposed design until hardware exists and decisions are made.
  - Later Phase 2B design should cover segmentation, firewall policy,
    multicast/discovery behavior, private access, and rollback.
  - Exit: Phase 2 remains open until Phase 2B is reviewed or deliberately
    deferred into a later roadmap phase.

- [x] Phase 3 - Host, Storage, and Backup Foundation
  - [x] Initial storage layout decision: create 80 GiB `/srv` LV and keep
    remaining VG space reserved.
  - [x] Apply and validate `/srv` mount on the server.
  - [x] Define target backup direction: future NAS with two disks in RAID1.
  - [x] Define temporary backup target: existing external `BACKUP_2TB` disk.
  - [x] Define and test first concrete backup/restore procedure.
  - [x] Safely check `BACKUP_2TB` after it was observed remounted read-only
    with `errors=remount-ro`.
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
