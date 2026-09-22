# Current State

This file contains only verified current facts or explicitly marked observations
pending revalidation.

## Repository

- Current directory: `/home/kiyana/Desktop/project-home`.
- Original source documents are now preserved at:
  - `docs/reference/original-context/CONTEXT_PROJECT_HOME.txt`
  - `docs/reference/original-context/CONTEXTO_OPENPANEL_Y_PROJECT_DEVOPS_PARA_CHATGPT.txt`
  - `docs/reference/original-context/ARQUITECTURA_OBJETIVO_HOME_INFRASTRUCTURE.md`
- Historical source documents are preserved under `docs/reference/original-context/`.
- Git-specific state is tracked in `.ai/contexts/GIT.md`.

## Project Phase

- The project is still designing the repository/context system.
- Infrastructure implementation has not started.
- No Kubernetes, Terraform, Ansible, GitOps, NAS, dedicated firewall/router,
  VLAN, AdGuard Home, SOPS/age, GitHub Actions, Argo CD, or Argo Rollouts
  implementation has been created by this repository.

## Initial Hardware And OS Target

- Initial physical server: existing Acer Aspire E5-571G.
- CPU: Intel i3-4005U.
- Memory: 12 GB RAM.
- Storage: Kingston 240 GB SSD.
- Selected installation target: Ubuntu Server 26.04.1 LTS.
- The Ubuntu Server 26.04.1 LTS ISO has been downloaded and checksum-verified.
- Ubuntu Server is not currently installed.

## Previously Observed Network Information

The old context contains useful network observations, but they are not promoted
to verified current state. They should be revalidated before being used for
implementation decisions.

Previously observed:

- Nokia G-010G-P appeared to be the ONT.
- Main router/gateway appeared to be Sagemcom F@ST 5657.
- Two ZTE ZXHN H3601 V9.1 devices appeared to operate in repeater mode.
- LAN appeared to be `192.168.1.0/24` with gateway `192.168.1.1`.
- Smart View / casting instability was suspected to relate to repeater or
  wireless backhaul behavior, but the root cause was not proven.
