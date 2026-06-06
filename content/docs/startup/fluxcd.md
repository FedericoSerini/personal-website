---
title: FluxCD
weight: 2
prev: /docs/startup/kubernetes/
next: /docs/apps/
---

[Flux](https://fluxcd.io/) is the GitOps engine that keeps the cluster in sync with the repository.
Every change to the homelab goes through a Git commit—Flux polls the repo and reconciles
the cluster state automatically.

## Prerequisites

- A running Kubernetes cluster
- A GitHub personal access token with `repo` permissions
- `flux` CLI installed (`brew install fluxcd/tap/flux`)

## Bootstrapping Flux

```shell
export GITHUB_TOKEN=<your-token>
export GITHUB_USER=<your-username>

flux bootstrap github \
  --owner=$GITHUB_USER \
  --repository=homelab \
  --branch=main \
  --path=./clusters/staging \
  --personal
```

This installs the Flux controllers in the `flux-system` namespace and commits the manifests
to the repository. From this point, any push to `main` triggers a reconciliation.

## Kustomizations

The cluster is split into four Flux Kustomizations, each with its own sync interval:

| Kustomization | Path | Interval |
|---|---|---|
| apps | `apps/staging` | 15m |
| databases | `databases` | 15m |
| infra-controllers | `infrastructure/controllers/staging` | 1h |
| monitoring | `monitoring/controllers/staging` | 1h |

## Important Notes

**Flux tracks `main` only.** There is no staging/dev branch reconciliation—everything
flows through `main`. For urgent fixes, `kubectl patch` is the fast path, but the
change must land in Git to persist across the next reconciliation.

**HelmRelease values** are passed via `valuesFrom` referencing kustomize hash-named ConfigMaps.
If a ConfigMap is updated, the pod won't restart automatically—you need to delete the pod manually
after Flux reconciles the new ConfigMap.

**Manual reconcile** (useful when you don't want to wait for the next interval):

```shell
flux reconcile kustomization apps --with-source
```
