# Task Prompt

Use this prompt when starting a new Home Lab task.

```text
Always read:
1. AGENTS.md

Then select context by task:
- .ai/contexts/PROJECT.md: project purpose/scope
- .ai/contexts/CURRENT_STATE.md: tasks depending on deployed/current reality
- .ai/contexts/GIT.md: Git, repository, GitHub, branches, commits, tags,
  versioning, releases, dependency automation, or repository lifecycle tasks
- .ai/contexts/DECISIONS.md: architectural/design decisions
- .ai/contexts/ROADMAP.md: planning/progress
- .ai/contexts/TARGET_ARCHITECTURE.md: architecture/design work
- docs/*: targeted deep/reference information only

Do not load a context file unless it can materially affect the task.

Follow:
inspect -> understand -> propose -> review -> implement -> test -> document

Do not install software, change networking, initialize infrastructure, or create
implementation directories unless the task explicitly requires it.

Separate:
- verified current facts
- intended future architecture
- decisions
- proposals
- reference-only historical material
```
