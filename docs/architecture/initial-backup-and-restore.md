# Initial Backup And Restore Model

This document records the initial backup and restore model for the Home Lab.
It separates the final target architecture from the temporary backup approach
available before the NAS exists.

## Target Direction

The intended future storage and backup target is a NAS with two disks in RAID1.
The NAS is expected to provide safer shared storage and backup capacity for the
Home Lab than the initial server SSD alone.

RAID1 improves availability after a single disk failure, but it is not a backup
by itself. It does not protect against accidental deletion, corruption,
misconfiguration, malware, theft, fire, or bad application changes. Backup and
restore policy remains required even when the NAS exists.

The exact NAS model, disk model/capacity, filesystem, snapshot policy, shares,
backup tool, retention, encryption, and off-site strategy remain TBD.

## Current Temporary Backup Target

Until the NAS exists, the available temporary backup target is an existing
external USB disk connected to the workstation.

Observed current target:

| Item | Value |
|---|---|
| Device model | TOSHIBA MQ USB disk |
| Usable size | approximately 1.8 TiB |
| Partition | `/dev/sda1` on the workstation at observation time |
| Filesystem | exFAT |
| Label | `BACKUP_2TB` |
| UUID | `7065-B2A3` |
| Mountpoint | `/media/kiyana/BACKUP_2TB` |
| Used at observation | approximately 31G |
| Available at observation | approximately 1.8T |

The Home Lab provisional area on that disk is:

```text
/media/kiyana/BACKUP_2TB/HomeLab/
├── backups/
├── exports/
├── restore-tests/
└── README.txt
```

This temporary target is non-destructive. The disk must not be reformatted,
repartitioned, or repurposed for the Home Lab without an explicit future
decision.

## Current Rules

- Use the external disk only as a provisional backup target until the NAS exists.
- Do not use the 64 GB USB installer/rescue device as the primary Home Lab
  backup target.
- Do not store plaintext secrets on the external disk unless protected by a
  separate reviewed mechanism.
- Prefer packaged backups, exports, and documented restore artifacts because the
  disk uses exFAT and does not preserve Linux ownership/permissions like ext4.
- Do not use the external disk as active application storage for the server.
- Do not treat this temporary disk as the final backup architecture.

## Initial Backup Scope

Initial backup scope should stay small until real services exist:

- `/srv` persistent Home Lab data
- selected host configuration exports from `/etc`, not a blind full-system copy
- package/bootstrap notes needed to rebuild the host
- restore notes and validation evidence

Git already stores project documentation and reproducible configuration that is
safe to commit. Secrets must not be stored in plaintext in Git or on the
external disk.

## Restore Principle

The minimum recovery path should be:

```text
fresh Ubuntu Server install
  -> restore SSH/admin access
  -> clone the Git repository
  -> recreate LVM/storage layout including /srv
  -> restore backed-up /srv data and selected config exports
  -> validate service or host functionality
```

The first concrete backup/restore procedure has been defined and tested using
this temporary target.

## First Concrete Backup/Restore Test

On 2026-09-22, the first concrete `/srv` backup/restore smoke test was run.

Backup source:

```text
homelab-server-01:/srv/
```

Backup destination:

```text
/media/kiyana/BACKUP_2TB/HomeLab/backups/2026-09-22-srv-smoke-test/srv/
```

`/srv/lost+found/` is excluded because it is an ext4 system directory owned by
root and is not part of Home Lab application data. The first `rsync` attempt
returned code 23 only because `/srv/lost+found/` could not be read by the
normal SSH user. Re-running with `--exclude='lost+found/'` and
`--delete-excluded` completed with exit code 0.

Restore validation was performed from the external-disk backup into:

```text
/tmp/homelab-restore-test-2026-09-22/srv/
```

The restored smoke-test file matched the source file hash:

```text
220e56aedf4ccd66a512f21f1daefe0b070954bc6871fc45f0749b75e13789f1
```

The external exFAT disk was briefly observed mounted read-only with
`errors=remount-ro` after a USB disconnect/cache synchronization failure. A
non-repair check with `fsck.exfat -n /dev/sda1` reported the filesystem clean,
and the disk was later observed mounted read/write again.

Restore evidence was then written to:

```text
/media/kiyana/BACKUP_2TB/HomeLab/restore-tests/2026-09-22-srv-smoke-test/srv/
```

The backup and restore-test file hashes matched:

```text
220e56aedf4ccd66a512f21f1daefe0b070954bc6871fc45f0749b75e13789f1
```

Do not format, repartition, or destructively repair this disk without a future
explicit decision because it contains pre-existing non-Home-Lab data.
