# OpenPanel / Project-DevOps Reference

This document is historical reference only. It describes previous work and
patterns that may inform `home-infrastructure`, but it is not current Home Lab
state.

The previous system separated application and infrastructure:

- `openpanel/`: an OpenPanel application fork.
- `Project-DevOps/`: local development, Kubernetes, GitOps, CI/CD,
  observability, security, backups, and Terraform.

Useful patterns to preserve or adapt:

- clear repository organization
- infrastructure/application separation where useful
- GitHub Actions validation
- dependency automation and lock files
- Terraform environment/module discipline where Terraform is justified
- Kubernetes base/overlay concepts where Kubernetes is justified
- Argo CD GitOps
- Argo Rollouts only where progressive delivery adds value
- security scanning
- observability
- backup/restore
- reproducible deployments
- documentation and architecture diagrams

Historical technologies included Docker Compose, Terraform, Minikube, EKS
design, Argo CD, Argo Rollouts, Kustomize, Prometheus, Alertmanager, Grafana,
Loki, Promtail, Tempo, Velero, MinIO, Sealed Secrets, cert-manager, Gitleaks,
kube-linter, GitHub Actions, Dependabot, backup/restore scripts, immutable
release tags, and GitOps image tag updates.

Do not infer that any of those technologies are implemented or approved for the
current Home Lab unless `.ai/contexts/DECISIONS.md` says so.

The original source document remains at:

```text
docs/reference/original-context/CONTEXTO_OPENPANEL_Y_PROJECT_DEVOPS_PARA_CHATGPT.txt
```
