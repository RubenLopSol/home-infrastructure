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
- Initial LAN addressing for the server has been stabilized with a router DHCP reservation.
- Phase 2A current-network baseline is complete for the hardware currently available.
- Phase 2B future network design is pending future hardware purchases and decisions.
- Ubuntu Server base installation is complete.
- Phase 3 host, storage, and backup foundation is complete.
- Phase 4 application-platform design has started with a proposal document at
  `docs/architecture/application-platform-phase-4.md`.
- Initial k3s single-node Kubernetes platform implementation has started on
  `homelab-server-01`.
- No Terraform, Ansible, GitOps, NAS, dedicated firewall/router,
  managed switch, dedicated wired AP, UPS, VLAN, AdGuard Home, SOPS/age,
  GitHub Actions, Argo CD, Argo Rollouts, ingress controller, default storage
  class, or production workload implementation has been created by this
  repository.
- Current available network hardware is limited to the ISP/router, existing
  repeaters, and `homelab-server-01`. Additional network/storage/power hardware
  has not been purchased yet.

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
- Phase 3 storage change has been applied and validated.
- Logical volume `srv-lv` exists in `ubuntu-vg` with size 80 GiB.
- `/srv` is formatted as ext4 with label `homelab-srv` and mounted from
  `/dev/mapper/ubuntu--vg-srv--lv`.
- `/srv` filesystem size is approximately 79G, with approximately 75G available
  immediately after creation.
- `ubuntu-vg` has approximately 40.52 GiB free after creating `srv-lv`.

## Initial Network And SSH State

- Ethernet interface detected: `enp1s0f1`.
- Alternative interface name: `enxf0761cbe7fd1`.
- NIC driver: `r8169`.
- NIC: Realtek RTL8111/8168/8211/8411 PCI Express Gigabit Ethernet.
- MAC address: `f0:76:1c:be:7f:d1`.
- Link: 1 Gbps, full duplex.
- Interface state: routable/configured/online.
- Networking is managed through Netplan and systemd-networkd.
- Current network configuration: DHCPv4.
- Current IPv4: `192.168.1.181/24`.
- Router/DHCP server/gateway: `192.168.1.1`.
- No static IP has been configured on Ubuntu.
- Router DHCP reservation is configured and validated for MAC
  `f0:76:1c:be:7f:d1` -> `192.168.1.181`.
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

## Initial Application Platform State

- k3s is installed on `homelab-server-01`.
- k3s release installed from the stable channel: `v1.36.4+k3s1`.
- k3s is running as a systemd service and is enabled at boot.
- Node name: `homelab-server-01`.
- Node status: `Ready`.
- Node role: `control-plane`.
- Kubernetes internal node IP: `192.168.1.181`.
- Container runtime: `containerd://2.3.4-k3s1.36`.
- Packaged CoreDNS is enabled and running.
- Packaged metrics-server is enabled and running.
- Packaged Traefik was disabled during installation.
- Packaged ServiceLB was disabled during installation.
- Packaged local-storage provisioner was disabled during installation.
- `kubectl get storageclass` returned no resources after installation.
- `kubectl get ingressclass` returned no resources after installation.
- Initial observed Kubernetes node metrics:
  - CPU: `154m`, approximately 3%.
  - Memory: `703Mi`, approximately 6%.
- Initial observed system pod metrics:
  - CoreDNS: `5m` CPU, `11Mi` memory.
  - metrics-server: `15m` CPU, `18Mi` memory.
- Initial `/var/lib/rancher/k3s` size: `467M`.
- UFW remains active with OpenSSH allowed, Kubernetes API `6443/tcp` allowed
  from `192.168.1.0/24`, pod CIDR `10.42.0.0/16` allowed, and service CIDR
  `10.43.0.0/16` allowed.
- `/etc/sysctl.d/99-k3s.conf` sets:
  - `net.ipv4.ip_forward = 1`
  - `net.bridge.bridge-nf-call-iptables = 1`
  - `net.bridge.bridge-nf-call-ip6tables = 1`
- Kernel modules `overlay` and `br_netfilter` were loaded and verified.

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
- APT upgrades were applied successfully.
- `rust-coreutils` remains pending due to Ubuntu phased rollout and was not
  forced.
- UEFI CA firmware/signature database was updated from 2011 to 2023.
- UEFI dbx was updated from 20250902 to 20260402.
- The host rebooted successfully after firmware updates.
- SSH access using the key/alias was verified after reboot.
- Secure Boot remains enabled.
- `smartmontools` is installed.
- SMART health for `/dev/sda` was collected and overall self-assessment is
  PASSED.
- UFW is active with default incoming deny, outgoing allow, routed disabled, and
  OpenSSH allowed for IPv4/IPv6.
- SSH access using the workstation key/alias was verified after enabling UFW.
- Initial user/sudo/time inventory is complete.
- User `ruben` is in groups: `ruben`, `adm`, `cdrom`, `sudo`, `dip`,
  `plugdev`, `users`, `lxd`.
- System timezone is `Etc/UTC`; NTP is active and synchronized.
- User `ruben` has reversible shell usability helpers installed in
  `~/.bashrc`, with the previous file backed up as
  `~/.bashrc.backup-20260924-111133`.
- The following have not yet been executed/configured:
  - remaining host hardening
  - Docker/Podman
  - Kubernetes
  - observability
  - GitOps configuration
  - forced upgrade of `rust-coreutils`, pending Ubuntu phased rollout


## Initial Backup State

- Target future storage/backup direction: NAS with two disks in RAID1.
- The NAS has not been purchased or implemented yet.
- RAID1 is not treated as a substitute for backup.
- Current provisional backup target: existing external TOSHIBA USB disk mounted
  on the workstation as `BACKUP_2TB` at `/media/kiyana/BACKUP_2TB`.
- The external disk uses exFAT and has approximately 1.8 TiB usable capacity.
- Provisional Home Lab backup area exists at
  `/media/kiyana/BACKUP_2TB/HomeLab/`.
- First `/srv` backup/restore smoke test was completed on 2026-09-22:
  - backup path:
    `/media/kiyana/BACKUP_2TB/HomeLab/backups/2026-09-22-srv-smoke-test/srv/`
  - `/srv/lost+found/` is excluded from the backup because it is an ext4 system
    directory owned by root and is not Home Lab application data.
  - restore was validated from the external-disk backup into
    `/tmp/homelab-restore-test-2026-09-22/srv/`.
  - restore evidence was also written to:
    `/media/kiyana/BACKUP_2TB/HomeLab/restore-tests/2026-09-22-srv-smoke-test/srv/`.
  - source and restored smoke-test file SHA256 matched:
    `220e56aedf4ccd66a512f21f1daefe0b070954bc6871fc45f0749b75e13789f1`.
- During restore validation, `BACKUP_2TB` was briefly observed mounted
  read-only with `errors=remount-ro` after a USB disconnect/cache
  synchronization failure. `fsck.exfat -n /dev/sda1` reported the filesystem
  clean, and the disk was later observed mounted read/write again.
- The 64 GB USB device is not the primary Home Lab backup target.
- No final backup tool, retention policy, encryption model, NAS share layout, or
  off-site strategy has been selected yet.

## Previously Observed Network Information

The old context contains useful network observations, but they are not promoted
to verified current state. They should be revalidated before being used for
implementation decisions.

Current verified network inventory is tracked in
`docs/architecture/current-network-inventory.md`.

Verified current network facts:

- `homelab-server-01` is connected by Ethernet on `enp1s0f1`.
- Interface altname: `enxf0761cbe7fd1`.
- NIC driver: `r8169`.
- NIC: Realtek RTL8111/8168/8211/8411 PCI Express Gigabit Ethernet.
- Link: 1 Gbps, full duplex.
- Server MAC: `f0:76:1c:be:7f:d1`.
- Server IPv4: `192.168.1.181/24` via DHCPv4.
- DHCP reservation validated: router binds `f0:76:1c:be:7f:d1` to
  `192.168.1.181`.
- Router model: Sagemcom F@st 5670Eth_EKT.
- LAN subnet: `192.168.1.0/24`.
- DHCP pool: `192.168.1.128` - `192.168.1.254`.
- DHCP lease time: 86400 seconds.
- Gateway/DHCP server: `192.168.1.1`, MAC `08:7b:12:dc:24:07`.
- DNS via DHCP: `77.26.11.233`, `212.142.173.65`.
- Workstation observed from server: `192.168.1.140`, MAC `e4:54:e8:3a:b9:3f`.
- Gateway and Internet/DNS connectivity have been verified.
- DHCP renewal validation succeeded; the server retained `192.168.1.181/24`.
- For this phase, only a DHCP reservation was changed. WAN firewall, port
  forwarding, DMZ, public exposure, VLANs, global DNS, DHCP pool, and router
  gateway were not changed.

Previously observed:

- Nokia G-010G-P appeared to be the ONT.
- Main router/gateway appeared to be Sagemcom F@ST 5657 in older notes;
  current verified router model is Sagemcom F@st 5670Eth_EKT.
- Two ZTE ZXHN H3601 V9.1 devices appeared to operate in repeater mode.
- LAN appeared to be `192.168.1.0/24` with gateway `192.168.1.1`.
- Smart View / casting instability was suspected to relate to repeater or
  wireless backhaul behavior, but the root cause was not proven.
