# Context Update Prompt

Use after meaningful project work.

```text
At the final document step, decide whether project truth materially changed.
If nothing materially changed, do not modify context merely for activity
logging.

Update context with strict separation:

- .ai/contexts/CURRENT_STATE.md:
  verified current facts only

- .ai/contexts/TARGET_ARCHITECTURE.md:
  intended future architecture only

- .ai/contexts/DECISIONS.md:
  DECIDED / PROPOSED / TBD / REJECTED

- .ai/contexts/GIT.md:
  Git/repository state, remotes, hosting, branches, commits, tags, versioning,
  releases, automation, and repository lifecycle decisions

- .ai/contexts/ROADMAP.md:
  phase progress and next work

- docs/:
  detailed architecture, ADRs, runbooks, reference, evidence

Keep loaded context small. Move detail to docs and retrieve it only when needed.
Do not promote old observations to current facts without revalidation.
Update only affected context files and avoid loading unrelated context.
```
