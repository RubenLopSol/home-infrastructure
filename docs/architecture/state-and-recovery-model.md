# State And Recovery Model

This document preserves the state ownership and recovery model from the
original project context. It is implementation-neutral: named technologies are
examples from the original architecture discussion, not current implemented
state unless `.ai/contexts/DECISIONS.md` says otherwise.

## Storage And Recovery Categories

Storage categories may include:

- application data
- media
- camera recordings
- backups
- configuration
- GitOps state

Backup architecture must distinguish:

- replication
- backup
- snapshot
- restore
- disaster recovery

A backup that has never been restored is not considered fully validated.

The long-term goal is to rebuild the platform from Git plus required external
data, secrets, and backups.

## GitOps State Is Not Application State

GitOps describes desired platform state; it is not a substitute for application
data. Every deployed component must classify its state and have an explicit
owner and recovery path.

State ownership categories:

1. Declarative configuration: deployment declarations, chart versions, values,
   overlays, dashboards, alerts, policies, and infrastructure configuration.
   These belong in Git and must be versioned.

2. Secrets and cryptographic identity: API tokens, passwords, TLS material,
   WireGuard peer keys, MQTT credentials, and encryption keys. These must never
   be stored in plaintext in Git. They require encrypted secret management and a
   separately protected recovery copy of the decryption material.

3. Persistent application state: databases, application configuration, paired
   device state, automations, integrations, and recordings. This must be stored
   on durable storage outside a container filesystem and backed up.

4. External network-device configuration: router, firewall, switch, and AP
   configuration. This is outside Kubernetes and must have a documented, secure
   export/backup and restore procedure where the device supports it.

Container filesystems and ephemeral volumes must never be the only location for
data that is needed after restart or recovery. A service deployment must
explicitly enable and configure persistence before the service is accepted.

## Backup Policy Requirements

A backup policy should define:

- what is backed up
- frequency
- retention
- encryption where appropriate
- local copy
- off-site copy where possible
- restore testing

Target recovery exercise from the original context:

```text
Fresh host
    -> host bootstrap
    -> infrastructure provisioning
    -> application platform
    -> deployment reconciliation
    -> applications
    -> restore data
    -> verification
```

The desired result is a functioning home, not an empty set of freshly started
containers. Backups are not valid until their restore procedure has been
exercised and documented.

## Service-Specific Recovery Considerations

Home-critical services require service-specific recovery guarantees:

- Home Assistant: configuration, database, integrations, automations,
  dashboards, and paired-device state must be recoverable without re-adding
  devices wherever the integration permits it.
- MQTT: configuration, users, ACLs, certificates, and required persistence must
  be recoverable.
- AdGuard Home/DNS: configuration, filters, local records, rewrites, and client
  policy must be recoverable.
- Cameras/NVR: configuration, credentials, retention policy, and metadata must
  be recoverable; recording retention and backup scope are an explicit capacity
  decision.
- Zigbee/Z-Wave/Thread: coordinator and network backup material must be
  identified, protected, and restore-tested before relying on the network.

Recovery target for a total host failure:

```text
replacement host
   -> automated host/bootstrap configuration
   -> application platform and deployment reconciliation
   -> restore persistent data and secrets
   -> functional verification of home services
```
