---
title: FluxCD
weight: 2
prev: /docs/startup/kubernetes/
next: /docs/apps/
---

## 🚧 Under Construction

## Installing Flux

- Flux needs to be installed on the cluster to facilitate the GitOps reconciliation loop
- Prerequisites: Kubernetes cluster and GitHub personal access token

### Creating a GitHub Personal Access Token

- Go to GitHub Settings > Developer Settings > Personal Access Tokens
- Generate a new classic token with 'repo' permissions
- Store the token in an environment variable: export GITHUB_TOKEN=<your-token>
- Also export your GitHub username: export GITHUB_USER=<your-username>

### Installing Flux CLI

- Various installation methods available (e.g., Homebrew, curl)
- Verify installation with which flux

### Checking Cluster Compatibility

- Run flux check --pre to ensure the cluster meets Flux requirements

### Bootstrapping Flux on the Cluster

By using the command:

```shell
flux bootstrap github \
  --owner=$GITHUB_USER \
  --repository=pi-cluster \
  --branch=main \
  --path=./clusters/staging \
  --personal
```

installs Flux controllers and commits manifests to the specified repository

## Results

- Flux creates necessary manifests in the GitHub repository
- New namespace 'flux-system' is created on the cluster with Flux components
- GitOps controller is now active on the cluster

## Next Steps

- Ready to deploy applications using GitOps methodologies
