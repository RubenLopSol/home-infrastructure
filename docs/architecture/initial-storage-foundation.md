# Initial Storage Foundation

This document records the initial storage foundation for `homelab-server-01`.
It separates operating system storage from future persistent Home Lab data
without over-partitioning before the application platform and services are
selected.

## Current Disk Baseline

The host uses the full Kingston A400 240 GB SSD with LVM enabled.

Original layout before applying the Phase 3 storage change:

```text
Kingston SSD ~223.57 GiB
├── /boot/efi ~1.049 GiB FAT32
├── /boot     2 GiB ext4
└── LVM PV    ~220.518 GiB
    └── ubuntu-vg ~220.515 GiB
        ├── ubuntu-lv mounted at / : 100 GiB
        └── ~120.5 GiB free inside the VG
```

## Applied Layout

The Phase 3 storage change has been applied and validated:

```text
ubuntu-vg
├── ubuntu-lv   100 GiB   /
├── srv-lv       80 GiB   /srv
└── free        ~40.52 GiB   reserved for future allocation
```

`/srv` is formatted as ext4 with label `homelab-srv` and is mounted from
`/dev/mapper/ubuntu--vg-srv--lv`. The `/etc/fstab` entry uses the filesystem
UUID.

Observed after validation:

| Item | Value |
|---|---|
| LV | `ubuntu-vg/srv-lv` |
| Size | 80.00 GiB |
| Filesystem | ext4 |
| Label | `homelab-srv` |
| Mountpoint | `/srv` |
| Filesystem size | 79G |
| Available after creation | 75G |
| VG free after creation | `<40.52g` |

`/srv` is the initial root for persistent Home Lab data. The root filesystem
remains focused on the operating system and packages.

## Rationale

This layout provides a clear boundary between OS state and Home Lab data while
keeping the design simple. It avoids filling `/` with service data, supports a
separate backup policy for persistent data, and keeps free LVM capacity for
future growth or new volumes once real service requirements exist.

The project intentionally does not create many service-specific volumes yet.
Since this foundation was created, the initial application runtime has been
selected as k3s and the initial Kubernetes dynamic storage class has been
selected as `local-path-srv`, backed by `/srv/k3s/storage`. Backup tooling and
first production persistent services remain undecided.

## Applied Scope

The Phase 3 storage change only:

- created `ubuntu-vg/srv-lv` with size 80 GiB
- formatted it as ext4
- mounted it at `/srv`
- added a stable `/etc/fstab` entry by UUID
- validated the active `/srv` mount

`/var/lib`, container runtime data, Kubernetes data, databases, and application
data have not been moved.

## Future Use

Future directories under `/srv` may include service data, backup staging, or
restore work areas, but they should be created just in time when a concrete
service or workflow requires them.

Current Kubernetes use under `/srv`:

- `/srv/k3s/storage` is the data root for the `local-path-srv` StorageClass.
- Temporary smoke-test directories under `/srv/k3s-test` were used for initial
  persistence validation and then removed after backup/restore validation.
