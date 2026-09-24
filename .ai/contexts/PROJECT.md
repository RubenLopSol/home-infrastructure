# Project

`home-infrastructure` is a personal DevOps / Platform Engineering home lab. The
goal is to design, implement, operate, monitor, secure, version, upgrade, break,
recover, and document a small production-grade platform using real engineering
practices adapted to home scale.

Core principle:

```text
Production-grade practices, adapted to homelab scale.
```

The project has moved beyond repository/context-system design. The repository,
base Ubuntu Server host, `/srv` storage foundation, temporary backup/restore
path, and initial single-node k3s platform with `local-path-srv` storage are
implemented and documented. Production home services, GitOps, observability,
final secret management, NAS integration, and dedicated network/storage
hardware remain future work.

The platform should be versioned, reproducible, automated, observable, secure,
recoverable, testable, documented, maintainable, and simple enough to operate
correctly. Design comes before implementation.

Git will be the source of truth. Detailed history and architecture live under
`docs/`; frequently loaded AI context stays small under `.ai/contexts/`.

The previous OpenPanel / Project-DevOps work is useful reference material only.
It must not be treated as current Home Lab state.

A component is not complete merely because it runs. Completion requires, where
applicable, versioned configuration, pinned dependencies, secure secrets,
monitoring, backups, tested restore, documented recovery behavior, validation,
and GitOps-compatible operation.
