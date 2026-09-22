# Git Context

This is the canonical context for Git and repository-specific state, decisions,
and configuration.

## Current State

- Local project path: `/home/kiyana/Desktop/project-home`.
- The project is currently not a valid Git repository.
- No Git remote is configured.
- Hosting provider: TBD.

The existing `.git` directory was inspected and found to be:

- empty
- read-only
- mounted as a `tmpfs` mountpoint
- not a valid Git repository

It must be unmounted/removed before a normal repository can be initialized at
this path.

## Decisions

- Git will be the source of truth.
- Secrets must never be committed in plaintext.
- Reproducibility-affecting configuration should be versioned.
- Git-related configuration should be changed through this context when its
  state materially changes.

## Repository

- Local path: `/home/kiyana/Desktop/project-home`
- Remote: TBD
- Hosting: TBD
- Default branch: TBD

## Versioning

- Versioning strategy: TBD.
- Semantic Versioning is historical/proposed context only, not yet decided for
  this project baseline.
- Initial version/tag: TBD.

## Branching

TBD.

## Commit Convention

TBD.

## Release / Tagging

TBD.

## Automation

- GitHub Actions: PROPOSED.
- Dependency automation: PROPOSED/TBD.
- Branch protection: TBD.

## Open Questions

- When should the repository be initialized after context review?
- Which hosting provider should be used?
- What should the default branch be?
- What branching and commit conventions should be used?
- What versioning, release, and tagging policy should be adopted?
- Which repository automation should be introduced, and when?
