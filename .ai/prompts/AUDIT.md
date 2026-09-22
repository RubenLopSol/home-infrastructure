# Audit Prompt

Use to audit the repository/context system.

```text
Check for:
- current-state claims that are not verified
- target architecture mixed into current state
- old OpenPanel / Project-DevOps material treated as Home Lab state
- Git/repository state outside `.ai/contexts/GIT.md` without a clear reason
- missing DECIDED / PROPOSED / TBD / REJECTED labels
- obsolete hardware assumptions
- implementation directories created too early
- AI context files growing too large
- contradictions between AGENTS.md, `.ai/contexts/`, docs/, and `.ai/prompts/`
- decisions that should become ADRs

Report findings before proposing edits.
```
