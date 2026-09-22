# Git Context

This is the canonical context for Git and repository-specific state, decisions,
and configuration.

## Current State

- Local project path: `/home/kiyana/Desktop/project-home`.
- The project is a valid Git repository.
- Current branch: `main`.
- Remote: `origin`.
- Remote URL: `git@github.com:RubenLopSol/home-infrastructure.git`.
- Hosting provider: GitHub.
- Initial baseline commit: `01595d3` (`Initial project baseline`).
- Local `main` is tracking `origin/main`.

## Decisions

- Git will be the source of truth.
- Secrets must never be committed in plaintext.
- Reproducibility-affecting configuration should be versioned.
- Git-related configuration should be changed through this context when its
  state materially changes.

## Repository

- Local path: `/home/kiyana/Desktop/project-home`
- Remote: `origin`
- Remote URL: `git@github.com:RubenLopSol/home-infrastructure.git`
- Hosting: GitHub
- Default branch: `main`

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

- What branching and commit conventions should be used?
- What versioning, release, and tagging policy should be adopted?
- Which repository automation should be introduced, and when?
