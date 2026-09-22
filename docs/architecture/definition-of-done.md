# Definition Of Done

This document preserves the detailed Definition of Done from the original
project context. It is implementation-neutral and should be applied where
relevant to the component being introduced.

A component is not considered complete merely because it runs.

Where applicable, completion means:

- code/configuration is versioned
- dependencies are pinned/locked
- deployment is automated
- secrets are managed securely
- health checks exist
- monitoring exists
- logs are available
- alerts exist where useful
- backup strategy is defined
- persistent state is stored outside the container/ephemeral volume
- backup scope includes configuration, data, and required identities
- a restore has been tested with documented evidence
- recovery behavior is understood
- security controls are applied
- documentation exists
- failure mode is known
- rollback strategy exists
- CI validation passes, if CI exists for the component
- GitOps reconciliation works, if GitOps is used for the component
- manual steps are minimized and documented

For home-critical services, the Definition of Done additionally requires that a
replacement-host recovery restores the service's useful state rather than
presenting an empty, newly installed application.
