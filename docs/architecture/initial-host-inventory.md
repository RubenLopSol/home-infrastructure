# Initial Host Inventory

This document records verified facts from the initial operating system
installation checkpoint. It describes current state only; it does not imply that
the broader infrastructure platform has been implemented.

## Hardware

| Item | Value |
|---|---|
| Model | Acer Aspire E5-571G |
| CPU | Intel Core i3-4005U |
| RAM | 12 GB |
| Disk | Kingston A400 240 GB SSD |
| Use | Dedicated Home Lab server |

Observed system identity:

| Item | Value |
|---|---|
| Static hostname | `homelab-server-01` |
| Chassis | laptop |
| Hardware vendor | Acer |
| Hardware model | Aspire E5-571G |
| Hardware SKU | `Aspire E5-571G_0867_V1.32` |
| Hardware version | `V1.32` |
| Firmware version | `V1.32` |
| Firmware date | 2015-09-15 |

## Operating System

| Item | Value |
|---|---|
| OS | Ubuntu Server 26.04.1 LTS |
| Version codename | `resolute` |
| Kernel observed after first boot | `7.0.0-31-generic` |
| Architecture | `x86_64` |
| Boot mode | UEFI |
| Graphical environment | Not installed |
| Hostname | `homelab-server-01` |
| Initial admin user | `ruben` with sudo |
| KVM device | `/dev/kvm` present |

No passwords are documented or stored in this repository.

Observed kernel string:

```text
Linux homelab-server-01 7.0.0-31-generic #31-Ubuntu SMP PREEMPT_DYNAMIC Sat Aug 1 04:26:38 UTC 2026 x86_64 GNU/Linux
```

Observed APT sources:

- `security.ubuntu.com/ubuntu` for `resolute-security`.
- `es.archive.ubuntu.com/ubuntu` for `resolute`, `resolute-updates`, and
  `resolute-backports`.
- Enabled components observed: `main`, `restricted`, `universe`, `multiverse`.

Systemd state:

```text
running
```

## CPU And Memory

Observed CPU:

| Item | Value |
|---|---|
| Model | Intel Core i3-4005U CPU @ 1.70GHz |
| CPU architecture | `x86_64` |
| CPU op modes | 32-bit, 64-bit |
| Logical CPUs | 4 |
| Cores / threads | 2 cores / 4 threads |
| Socket(s) | 1 |
| Min frequency | 800 MHz |
| Max frequency | 1700 MHz |
| Virtualization | VT-x |
| L3 cache | 3 MiB |
| NUMA nodes | 1 |

Additional virtualization checks:

- Boot mode check confirmed UEFI through `/sys/firmware/efi`.
- `/dev/kvm` exists.
- `sudo dmesg | grep -Ei 'kvm|vmx|virtualization'` returned no matching lines
  at the time of testing.

Observed memory:

| Item | Value |
|---|---|
| RAM total | 11 GiB visible to OS |
| RAM used at observation | 558 MiB |
| RAM available at observation | 10 GiB |
| Swap total | 4.0 GiB |
| Swap used at observation | 0 B |

## Installation Options

| Option | Value |
|---|---|
| Ubuntu Server variant | Standard, not minimized |
| Third-party drivers | Not installed |
| Ubuntu Pro | Omitted |
| Featured server snaps | None |
| MicroK8s/runtime/orchestrator | Not installed |

The host is intentionally kept clean after OS installation. Infrastructure
components will be added later only when explicitly approved, reproducible, and
documented.

## Storage

The previous Windows 10 installation was removed and the full SSD was used for
Ubuntu Server.

Installer choices:

- Use entire disk: enabled.
- LVM: enabled.
- LUKS/full-disk encryption: disabled.

Observed layout during installation:

```text
Kingston SSD ~223.57 GiB
├── /boot/efi ~1.049 GiB FAT32
├── /boot     2 GiB ext4
└── LVM PV    ~220.518 GiB
    └── ubuntu-vg ~220.515 GiB
        ├── ubuntu-lv mounted at / : 100 GiB
        └── ~120.5 GiB free inside the VG
```

The original free space inside `ubuntu-vg` was intentionally left unallocated
during installation. In Phase 3, an 80 GiB `srv-lv` was created for `/srv`,
leaving approximately 40.52 GiB free in the VG.

Observed block devices after installation:

| Device | Size | Type | Filesystem | Mountpoint | Model | Serial |
|---|---:|---|---|---|---|---|
| `sda` | 223.6G | disk | | | KINGSTON SA4 | `50026B768336ED8E` |
| `sda1` | 1G | part | vfat | `/boot/efi` | | |
| `sda2` | 2G | part | ext4 | `/boot` | | |
| `sda3` | 220.5G | part | LVM2_member | | | |
| `ubuntu--vg-ubuntu--lv` | 100G | lvm | ext4 | `/` | | |
| `ubuntu--vg-srv--lv` | 80G | lvm | ext4 | `/srv` | | |
| `sr0` | 190.3M | rom | iso9660 | | Slimtype DVD | `0080F0085100279DF101` |

Observed disk identity:

| Item | Value |
|---|---|
| Disk | `sda` |
| Model | `KINGSTON SA400S3` |
| Serial | `50026B768336ED8E` |
| Size | `223.6G` |
| Rotational | `0` / non-rotational |
| Transport | SATA |
| Device state | `running` |

`smartmontools` is installed and SMART health has been collected.

SMART summary:

| Item | Value |
|---|---|
| SMART support | Available and enabled |
| Overall health self-assessment | PASSED |
| Power-on hours | 213 |
| Power cycle count | 344 |
| Temperature | 25 C |
| Reallocated event count | 0 |
| Reported uncorrectable errors | 0 |
| SATA CRC error count | 0 |
| Unsafe shutdown count | 116 |
| Lifetime writes | 1506 GiB |
| Lifetime reads | 1353 GiB |
| TRIM | Available |

SMART error log and self-test log are not supported by this device.

Observed filesystem usage:

| Filesystem | Size | Used | Available | Use | Mountpoint |
|---|---:|---:|---:|---:|---|
| `/dev/mapper/ubuntu--vg-ubuntu--lv` | 98G | 7.5G | 86G | 9% | `/` |
| `/dev/mapper/ubuntu--vg-srv--lv` | 79G | 2.1M | 75G | 1% | `/srv` |
| `/dev/sda2` | 2.0G | 191M | 1.6G | 11% | `/boot` |
| `/dev/sda1` | 1.1G | 6.4M | 1.1G | 1% | `/boot/efi` |

Observed LVM state:

| Item | Value |
|---|---|
| PV | `/dev/sda3` |
| VG | `ubuntu-vg` |
| VG size | `<220.52g` |
| VG free | `<40.52g` |
| Root LV | `ubuntu-lv`, `100.00g` |
| Persistent data LV | `srv-lv`, `80.00g`, mounted at `/srv` |

## Networking

| Item | Value |
|---|---|
| Ethernet interface | `enp1s0f1` |
| Ethernet chipset | Realtek RTL8111/8168/8211/8411 PCI Express Gigabit Ethernet |
| NIC driver | `r8169` |
| Link | 1 Gbps, full duplex |
| Interface state | routable/configured/online |
| Network manager | Netplan + systemd-networkd |
| Current addressing | DHCPv4 with router DHCP reservation |
| IPv4 observed during install/first boot | `192.168.1.181/24` |
| MAC address | `f0:76:1c:be:7f:d1` |
| Interface altname | `enxf0761cbe7fd1` |
| IPv6 link-local | `fe80::f276:1cff:febe:7fd1/64` |
| Default gateway | `192.168.1.1` |
| Current DNS servers | `77.26.11.233`, `212.142.173.65` |
| Current DNS mode | `systemd-resolved` stub |
| Static IP | Not configured |
| Proxy | Not configured |
| Ubuntu archive mirror | Official Spanish Ubuntu mirror |
| Installer connectivity check | Successful |

Observed routes:

```text
default via 192.168.1.1 dev enp1s0f1 proto dhcp src 192.168.1.181 metric 100
192.168.1.0/24 dev enp1s0f1 proto kernel scope link src 192.168.1.181 metric 100
192.168.1.1 dev enp1s0f1 proto dhcp scope link src 192.168.1.181 metric 100
77.26.11.233 via 192.168.1.1 dev enp1s0f1 proto dhcp src 192.168.1.181 metric 100
212.142.173.65 via 192.168.1.1 dev enp1s0f1 proto dhcp src 192.168.1.181 metric 100
```

Connectivity checks:

| Target | Result | RTT avg |
|---|---|---:|
| `192.168.1.1` | 4/4 received, 0% packet loss | 4.281 ms |
| `1.1.1.1` | 4/4 received, 0% packet loss | 22.611 ms |
| `google.com` | 4/4 received, 0% packet loss | 38.486 ms |

These checks verify local gateway reachability, Internet reachability by IP, and
DNS resolution at the time of testing.

DHCP reservation state:

- Router: Sagemcom F@st 5670Eth_EKT at `192.168.1.1`.
- DHCP pool: `192.168.1.128` - `192.168.1.254`; lease time 86400 seconds.
- Reservation: MAC `f0:76:1c:be:7f:d1` -> IPv4 `192.168.1.181`.
- Router UI device name: `Add MAC Address`.
- DHCP renewal validation succeeded; after `sudo networkctl renew enp1s0f1`, the
  server retained `192.168.1.181/24`.

## SSH

OpenSSH Server was installed during OS installation.

Current initial state:

- No SSH key was imported during the installer.
- A dedicated workstation SSH key was created for this host:
  `~/.ssh/homelab-server-01`.
- The public key was installed in `~/.ssh/authorized_keys` for `ruben`.
- SSH key-based login from the workstation was verified.
- Workstation SSH alias `homelab-server-01` is configured and verified.
- SSH password authentication was disabled with:
  `/etc/ssh/sshd_config.d/10-homelab-hardening.conf`.
- SSH configuration validation with `sshd -t` succeeded before reload.
- A second SSH session using the key/alias was verified after reloading SSH.

Verified workstation access:

```bash
ssh homelab-server-01
```

Observed SSH service state:

| Item | Value |
|---|---|
| Service | `ssh.service` |
| Loaded | loaded, preset enabled, service disabled and socket-triggered |
| Active | active (running) |
| Listener | `0.0.0.0:22`, `[::]:22` |
| Recent auth | public key accepted for `ruben` from workstation `192.168.1.140` |

Firewall state:

| Item | Value |
|---|---|
| UFW | active |
| Logging | on (low) |
| Default incoming | deny |
| Default outgoing | allow |
| Routed | disabled |
| Allowed inbound | OpenSSH / `22/tcp` for IPv4 and IPv6 |

SSH access using the workstation key/alias was verified after enabling UFW.

Observed listening sockets:

| Protocol | Address | Port | Process |
|---|---|---:|---|
| TCP | `0.0.0.0` | 22 | `sshd` |
| TCP | `[::]` | 22 | `sshd` |
| TCP/UDP | `127.0.0.53%lo` | 53 | `systemd-resolved` |
| TCP/UDP | `127.0.0.54` | 53 | `systemd-resolved` |
| UDP | `192.168.1.181%enp1s0f1` | 68 | `systemd-networkd` |
| UDP | `127.0.0.1`, `[::1]` | 323 | `chronyd` |

## First Boot Observations

Local login succeeded as:

```text
ruben@homelab-server-01
```

Observed values:

- IPv4: `192.168.1.181`.
- Temperature: approximately 43 C.
- Root filesystem/LV size: approximately 97.87 GiB available as the `/`
  filesystem size.
- Initial `/` usage: approximately 7.6%.
- Initial RAM usage: approximately 3%.
- Initial swap usage: 0%.
- System initially reported 13 available updates and 2 firmware upgrades.
- APT upgrades were applied successfully.
- No reboot-required file was present after APT upgrades.
- `rust-coreutils` remains pending due to Ubuntu phased rollout and was not
  forced.
- Firmware updates for UEFI CA and UEFI dbx were applied successfully.
- The host rebooted successfully after firmware updates.
- SSH access using the key/alias was verified after reboot.
- Secure Boot remains enabled.

Observed APT upgrades applied:

| Package | Available version | Current version |
|---|---|---|
| `dmidecode` | `3.6-2ubuntu1` | `3.6-2build1` |
| `libaudit-common` | `1:4.1.2-1ubuntu0.1` | `1:4.1.2-1build1` |
| `libaudit1` | `1:4.1.2-1ubuntu0.1` | `1:4.1.2-1build1` |
| `libflashrom1` | `1.6.0-2ubuntu1.1` | `1.6.0-2ubuntu1` |
| `libnetplan1` | `1.2-1ubuntu5.1` | `1.2-1ubuntu5` |
| `mdadm` | `4.5-5ubuntu1.1` | `4.5-5ubuntu1` |
| `netplan-generator` | `1.2-1ubuntu5.1` | `1.2-1ubuntu5` |
| `netplan.io` | `1.2-1ubuntu5.1` | `1.2-1ubuntu5` |
| `python-apt-common` | `3.1.0ubuntu1.1` | `3.1.0ubuntu1` |
| `python3-apt` | `3.1.0ubuntu1.1` | `3.1.0ubuntu1` |
| `python3-netplan` | `1.2-1ubuntu5.1` | `1.2-1ubuntu5` |
| `sos` | `4.11.2-0ubuntu0.1` | `4.10.2-1` |
| `thermald` | `2.5.11-0ubuntu1.1` | `2.5.11-0ubuntu1` |

Observed firmware inventory highlights before firmware update:

- System firmware: Acer/Insyde, current version `856031344`.
- BIOS region: current version `V1.32`.
- CPU microcode device: Intel Core i3-4005U, current version `0x00000026`.
- SSD: KINGSTON SA400S37240G, current version `03070009`.
- UEFI dbx: current version `20250902`.

Observed firmware updates applied:

| Device | Update | Current | New | Urgency |
|---|---|---:|---:|---|
| UEFI CA | Secure Boot Signature Database Configuration Update | 2011 | 2023 | High |
| UEFI dbx | Secure Boot Forbidden Signature Database Update | 20250902 | 20260402 | High |

Firmware report upload was declined. Disabling future firmware report prompts
was attempted but failed due to authentication/polkit; this does not affect the
applied firmware updates.

## User, Sudo And Time

Observed identity:

| Item | Value |
|---|---|
| UID | `1000(ruben)` |
| GID | `1000(ruben)` |
| Groups | `ruben`, `adm`, `cdrom`, `sudo`, `dip`, `plugdev`, `users`, `lxd` |

The initial administrative user `ruben` is a member of the `sudo` group.

Observed time state:

| Item | Value |
|---|---|
| Local time | `Tue 2026-09-22 13:43:48 UTC` |
| Universal time | `Tue 2026-09-22 13:43:48 UTC` |
| RTC time | `Tue 2026-09-22 13:43:48` |
| Time zone | `Etc/UTC (UTC, +0000)` |
| System clock synchronized | yes |
| NTP service | active |
| RTC in local TZ | no |

## Not Yet Done

The following have not yet been executed or configured:

- hardening
- Docker/Podman
- Kubernetes
- observability
- GitOps configuration
The following remains pending:

- `rust-coreutils` update when phased rollout reaches this host.

## Current Checkpoint

The base operating system installation, initial SSH key hardening checkpoint,
initial updates/firmware, SMART collection, and initial host/network inventory
are complete.

Detailed physical network revalidation remains for the network design phase.
The initial router DHCP reservation is configured and validated.

Next checkpoint: continue baseline/hardening work, including firewall policy and
remaining host hardening.
