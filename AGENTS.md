# Home Lab Agent Rules

This repository is in the context-system design phase. Do not install, deploy,
configure networking, initialize infrastructure, or create implementation
directories unless the current task explicitly requires it.

Required workflow:

```text
inspect -> understand -> propose -> review -> implement -> test -> document
```

## Cost Policy

AI/token cost is a first-class project requirement.

- Use the cheapest available model sufficient for the task. Current preferred
  default: GPT-5.6 Luna with low reasoning.
- Never increase reasoning effort or switch to a more expensive model
  automatically.
- If the current model or reasoning level is insufficient, stop and ask for
  explicit approval.
- Before requesting escalation, reduce context, retrieve targeted files,
  decompose the task, use deterministic tools/scripts, and reuse verified
  information.
- Do not read the entire repository when targeted files are sufficient.

## Context Rules

- Keep frequently loaded context small.
- Do not update canonical context after every command, experiment, or failed
  attempt.
- Update context when project truth materially changes: a verified current fact
  changes, a proposal becomes or replaces a decision, roadmap/progress changes,
  or a domain gains enough independent state to justify its own context.
- Context update lifecycle: work/experiment -> validate -> accept result or
  decision -> update affected context -> later commit with the related project
  change.
- `.ai/contexts/*` describes current working truth, not chronological activity
  logs.
  Use `docs/adr/*` for important decision history/rationale, Git history for
  implementation/change history, and `docs/reference/*` for historical material.
- Do not silently erase meaningful architectural history. If an accepted
  decision is replaced, update canonical context, mark the previous decision as
  superseded where useful, and create/update an ADR when reasoning and
  consequences matter.
- Put detailed architecture, history, runbooks, and reference material under
  `docs/`.
- Treat OpenPanel / Project-DevOps material as reference only, not current Home
  Lab state.
- Put verified current facts in `.ai/contexts/CURRENT_STATE.md`.
- Put intended future architecture in `.ai/contexts/TARGET_ARCHITECTURE.md`.
- Put decisions in `.ai/contexts/DECISIONS.md` and label each as `DECIDED`,
  `PROPOSED`, `TBD`, or `REJECTED`.
- Git is the intended source of truth.
- Do not store secrets in Git.
- Domain-specific context files should be created only when a domain has enough
  independent state or decisions to justify one. They must be loaded only when
  they can materially affect the current task. Possible future examples include
  `.ai/contexts/HOST.md`, `.ai/contexts/NETWORK.md`,
  `.ai/contexts/KUBERNETES.md`, `.ai/contexts/BACKUP.md`, and
  `.ai/contexts/OBSERVABILITY.md`; do not create them until they are justified.

## Safety Rules

Never blindly modify:

- default gateway
- DHCP
- DNS
- firewall
- network interfaces
- Wi-Fi
- critical services
- Kubernetes resources
- Terraform infrastructure
- persistent volumes
- credentials
- boot or network configuration

Critical routing, DHCP, firewalling, and recovery access must remain independent
of Kubernetes unless a future reviewed decision explicitly changes that.
