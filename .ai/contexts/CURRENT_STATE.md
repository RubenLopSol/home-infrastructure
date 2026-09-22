# Current State

This file contains only verified current facts or explicitly marked observations
pending revalidation.

## Repository

- Current directory: `/home/kiyana/Desktop/project-home`.
- The project is now a valid Git repository.
- Current Git branch: `main`.
- Git remote: `origin` -> `git@github.com:RubenLopSol/home-infrastructure.git`.
- Initial baseline commit: `01595d3` (`Initial project baseline`).
- Original source documents are now preserved at:
  - `docs/reference/original-context/CONTEXT_PROJECT_HOME.txt`
  - `docs/reference/original-context/CONTEXTO_OPENPANEL_Y_PROJECT_DEVOPS_PARA_CHATGPT.txt`
  - `docs/reference/original-context/ARQUITECTURA_OBJETIVO_HOME_INFRASTRUCTURE.md`
- Historical source documents are preserved under `docs/reference/original-context/`.
- Git-specific state is tracked in `.ai/contexts/GIT.md`.

## Project Phase

- Phase 0 repository/context baseline is complete.
- Phase 1 initial host and basic network inventory is complete.
- Detailed physical network revalidation remains for the network design phase.
- Ubuntu Server base installation is complete.
- Infrastructure platform implementation has not started.
- No Kubernetes, Terraform, Ansible, GitOps, NAS, dedicated firewall/router,
  VLAN, AdGuard Home, SOPS/age, GitHub Actions, Argo CD, or Argo Rollouts
  implementation has been created by this repository.

## Initial Host

- Physical server: Acer Aspire E5-571G.
- Use: dedicated Home Lab server.
- CPU: Intel Core i3-4005U.
- Memory: 12 GB RAM.
- Storage: Kingston A400 240 GB SSD.
- Hostname: `homelab-server-01`.
- Initial administrative user: `ruben` with sudo.
- Do not document or store passwords.
- Installed OS: Ubuntu Server 26.04.1 LTS.
- Observed kernel after first boot: `7.0.0-31-generic`.
- Architecture: `x86_64`.
- Boot mode: UEFI.
- No graphical environment installed.
- The Ubuntu Server 26.04.1 LTS ISO has been downloaded and checksum-verified.
- The verified ISO was written to a 7.5 GB USB device for installation.
- Verified ISO SHA256:
  `cc8a95cde20f6ced61a322420de00f10cc3c90ced545daa46cb9c1a117f1d927`.

## Initial Storage State

- Previous Windows 10 installation was removed.
- Installer storage choice: use entire disk.
- LVM: enabled.
- LUKS/full-disk encryption: disabled.
- Observed layout during installation:
  - Kingston SSD: approximately 223.57 GiB.
  - `/boot/efi`: approximately 1.049 GiB FAT32.
  - `/boot`: 2 GiB ext4.
  - LVM PV: approximately 220.518 GiB.
  - Volume group: `ubuntu-vg`, approximately 220.515 GiB.
  - Logical volume `ubuntu-lv` mounted at `/`: 100 GiB.
  - Approximately 120.5 GiB remains free in the VG for later allocation.

## Initial Network And SSH State

- Ethernet interface detected: `enp1s0f1`.
- Ethernet chipset: Realtek Gigabit Ethernet.
- Current network configuration: DHCP.
- IPv4 observed during installation/first boot: `192.168.1.181/24`.
- No static IP has been configured.
- Pending post-install decision: study/configure DHCP reservation in the router
  for `homelab-server-01`.
- No proxy was configured.
- Ubuntu archive mirror: official Spanish Ubuntu mirror.
- Installer connectivity check completed successfully.
- OpenSSH Server was installed during OS installation.
- A dedicated workstation SSH key was created for this host:
  `~/.ssh/homelab-server-01`.
- The public key was installed for `ruben` on the server.
- SSH key-based login from the workstation was verified.
- Workstation SSH alias `homelab-server-01` is configured and verified.
- SSH password authentication has been disabled through
  `/etc/ssh/sshd_config.d/10-homelab-hardening.conf`.
- SSH configuration validation with `sshd -t` succeeded before reload.
- A second SSH session using the key/alias was verified after reloading SSH.

## First Boot Observations

- Local login succeeded as `ruben@homelab-server-01`.
- IPv4 observed: `192.168.1.181`.
- Temperature observed: approximately 43 C.
- Root filesystem/LV size observed: approximately 97.87 GiB available as the
  `/` filesystem size.
- Initial `/` usage observed: approximately 7.6%.
- Initial RAM usage observed: approximately 3%.
- Initial swap usage observed: 0%.
- System reported 13 available updates.
- System reported 2 devices with available firmware upgrades.
- Initial user/sudo/time inventory is complete.
- User `ruben` is in groups: `ruben`, `adm`, `cdrom`, `sudo`, `dip`,
  `plugdev`, `users`, `lxd`.
- System timezone is `Etc/UTC`; NTP is active and synchronized.
- The following have not yet been executed/configured:
  - `apt upgrade`
  - firmware upgrades
  - hardening
  - firewall
  - Docker/Podman
  - Kubernetes
  - observability
  - GitOps configuration
  - SMART health collection with `smartctl`

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
