# Project Creation Roadmap

This roadmap describes how to create the Home Lab project step by step. It
separates preparation work that can be done before hardware is fully ready from
implementation work that requires the real host, network, storage, and services.

The project can be prepared substantially before all hardware exists. The
repository, documentation, decisions, checklists, architecture, and validation
plans can be created first. The Home Lab is not considered implemented until the
real host, network, services, backups, and restores have been deployed and
verified.

## Phase 0 - Repository Baseline

Goal: make the repository itself reliable before infrastructure work starts.

Tasks:

- Resolve the existing `.git` placeholder.
- Initialize a valid Git repository.
- Decide default branch.
- Decide whether a remote is configured now or later.
- Create the first baseline commit.
- Keep README, `AGENTS.md`, `.ai/`, and `docs/` aligned.

Exit criteria:

- Git is a valid local source of truth.
- The baseline documentation is committed.
- No infrastructure implementation has been created prematurely.

## Phase 1 - Functional Scope

Goal: define what the Home Lab should actually provide.

Tasks:

- List intended service categories: DNS, remote access, Home Assistant, MQTT,
  observability, backups, personal services, media, cameras/NVR.
- Classify services as critical, useful, or experimental.
- Identify which services need persistence.
- Identify which services need backup and restore validation.
- Decide which services belong in the first implementation wave.

Exit criteria:

- There is a service inventory or equivalent scope document.
- Each early service has an initial reason to exist.
- No service is introduced just because the tool is fashionable.

## Phase 2 - Architecture Design

Goal: define the platform shape before implementation.

Tasks:

- Define layers: network, host, application platform, services, data,
  observability, backup, recovery.
- Decide which responsibilities must stay outside Kubernetes.
- Define how Git, validation, automation, and GitOps should relate.
- Define the state and recovery model for stateful services.
- Create ADRs when a decision has important consequences.

Exit criteria:

- Architecture documentation explains the intended platform.
- Major decisions are recorded or explicitly marked as proposed/TBD.
- Current state and target architecture are not mixed.

## Phase 3 - Pre-Hardware Preparation

Goal: prepare repeatable implementation work before touching the real host.

Tasks:

- Prepare an Ubuntu Server installation checklist.
- Prepare a host inventory checklist.
- Prepare a network revalidation checklist.
- Prepare a backup/restore matrix template.
- Prepare security and hardening notes.
- Define what evidence is needed to mark each phase complete.

Exit criteria:

- The next physical steps are clear and reviewable.
- There is no need to improvise the first host setup.
- No implementation directories are created until there is real content for
  them.

## Phase 4 - Initial Host Installation

Goal: install and verify the first physical Home Lab host.

Current status: base Ubuntu Server installation is complete. Full inventory and
post-install baseline work are still in progress.

Tasks:

- Install Ubuntu Server 26.04.1 LTS on the Acer Aspire E5-571G. Completed.
- Record CPU, RAM, disk, network interfaces, firmware/BIOS notes, and storage.
- Check disk health, SMART data where available, temperatures, and basic
  stability.
- Validate network connectivity, DNS, SSH, firewall state, and virtualization
  support.
- Document the verified host state.

Exit criteria:

- The host has a factual inventory.
- The host can be accessed and administered safely.
- Hardware or network blockers are documented before platform work starts.

## Phase 5 - Host Foundation

Goal: establish the minimum reliable base for later services.

Tasks:

- Define users, SSH access, package/update policy, and basic hardening.
- Define local storage layout.
- Decide what host configuration should be automated and with which tool.
- Establish basic host monitoring if approved.
- Establish an initial backup path for important host configuration.

Exit criteria:

- The host baseline is documented and reproducible enough for the phase.
- Manual steps are minimized or documented.
- Important host state has an initial recovery path.

## Phase 6 - Network And Private Access Design

Goal: understand and design the home network before changing critical routing.

Tasks:

- Phase 2A: document the current router/server LAN baseline using existing
  hardware. Completed for the current hardware set.
- Document current network facts separately from target design.
- Keep dedicated router/firewall, managed switch, VLANs, NAS, UPS, and wired APs
  as future/proposed until hardware exists and decisions are made.
- Phase 2B: design future network segmentation, firewall policy,
  multicast/discovery behavior, private access, and rollback after future
  hardware decisions.
- Revalidate the historical Smart View/casting issue if still relevant.

Exit criteria:

- Phase 2A current network baseline is documented with existing hardware.
- Phase 2B remains explicitly pending until hardware and decisions exist.
- Network changes have a reviewed rollback plan before implementation.
- Critical network functions are not moved blindly.
- Remote access design does not expose management services publicly.

## Phase 7 - Storage, Backup, And Recovery Foundation

Goal: define how data survives failures before adding important services.

Tasks:

- Define storage categories: configuration, secrets, application data, media,
  recordings, backups.
- Define backup targets and off-site strategy.
- Define retention and encryption requirements.
- Define restore tests for early services.
- Decide when NAS or other backup/storage hardware is needed.

Exit criteria:

- There is a backup/restore matrix.
- A backup is not considered valid until restore has been tested.
- Stateful services have an explicit recovery path before they become critical.

## Phase 8 - Application Platform Decision

Goal: decide whether Kubernetes enters the first implementation and how.

Tasks:

- Decide whether Kubernetes is appropriate for the current host and services.
- Select Kubernetes distribution and version if approved.
- Define storage, ingress, namespaces, resource limits, security contexts, and
  network policies at the level needed for the first deployment.
- Deploy only a small test workload first, with persistence if stateful.

Exit criteria:

- The platform choice is documented.
- A test workload proves the basic deployment and persistence model if
  Kubernetes is introduced.
- Kubernetes is not responsible for routing, DHCP, firewall, or recovery access.

## Phase 9 - GitOps And Delivery Lifecycle

Goal: make application/platform changes reproducible from Git.

Tasks:

- Introduce CI/validation if approved.
- Introduce Argo CD if Kubernetes and GitOps are approved.
- Define deployment conventions.
- Pin versions where reproducibility depends on them.
- Avoid normal persistent changes through manual apply commands.

Exit criteria:

- At least one approved workload can be deployed reproducibly from Git.
- Validation catches basic errors before changes are applied.
- Manual deployment steps are documented or removed.

## Phase 10 - Observability, Security, And Secrets

Goal: make failures visible and access controlled.

Tasks:

- Add host/platform/service metrics where approved.
- Add logs and dashboards where useful.
- Add alerts that correspond to real failure modes.
- Choose and implement encrypted secret management.
- Define TLS and access-control approach.
- Verify that secrets are not stored in plaintext in Git.

Exit criteria:

- Important failures can be detected.
- Secrets have a recovery model.
- Access follows least privilege where practical.

## Phase 11 - First Home Services

Goal: introduce useful services only after persistence and recovery are clear.

Tasks:

- Add DNS/local filtering if approved and if fallback exists.
- Add private remote access if approved.
- Add Home Assistant and MQTT when backup/restore expectations are clear.
- Add personal services such as file sync or media only when storage and backup
  scope are defined.
- Defer cameras/NVR until retention, storage, network, and privacy requirements
  are real.

Exit criteria:

- Each service has persistence, backup, restore, monitoring, and access-control
  expectations documented.
- Home-critical services can be restored to useful state, not just redeployed
  empty.

## Phase 12 - Reliability And Continuous Improvement

Goal: improve the platform through controlled failure and upgrade practice.

Tasks:

- Run restore exercises.
- Test service failure, bad deployment, disk pressure, DNS failure, and remote
  access failure where safe.
- Write runbooks for known incidents.
- Record important architecture changes as ADRs.
- Review cost, power, noise, complexity, and hardware limits before expanding.

Exit criteria:

- Reliability claims are supported by evidence.
- Operational knowledge is documented.
- Future hardware purchases are based on measured need.
