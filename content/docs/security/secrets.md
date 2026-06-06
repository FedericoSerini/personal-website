---
title: Secrets
weight: 2
prev: /docs/security/falco
next: /docs/security/keycloak
---

Secrets are encrypted at rest using [SOPS](https://github.com/getsops/sops)
with [Age](https://age-encryption.org/) as the encryption backend.
Encrypted secret files are committed directly to the Git repository—
Flux decrypts them at reconciliation time using the `sops-age` secret stored in the cluster.

## Why SOPS + Age

- No external secrets manager to operate or pay for
- Secrets live in Git alongside the manifests that use them—same source of truth
- Age keys are simple: a single keypair, no PKI
- SOPS encrypts only the `data`/`stringData` fields, leaving the rest of the YAML readable

## Creating a New Secret

```shell
# 1. Generate the secret manifest (never apply this raw)
kubectl create secret generic <name> \
  --from-literal=KEY=VALUE \
  --dry-run=client -o yaml > secret.yaml

# 2. Encrypt it with SOPS
sops --age=$AGE_PUBLIC \
  --encrypt \
  --encrypted-regex '^(data|stringData)$' \
  --config clusters/staging/.sops.yaml \
  --in-place secret.yaml

# 3. Move into the right app folder and commit
mv secret.yaml apps/staging/<app>/secret.yaml
```

The `.sops.yaml` file in `clusters/staging/` tells SOPS which Age public key to use,
so you don't need to pass `--age` explicitly once it's configured.

## Decryption in the Cluster

Flux's `kustomize-controller` handles decryption transparently. The Age private key lives in the
`sops-age` Kubernetes secret in the `flux-system` namespace. Flux never logs or exposes
the decrypted values—they go straight into the target Secret resource.

**Never decrypt secrets in the terminal.** If you need to inspect a value, use
`kubectl get secret <name> -n <ns> -o jsonpath='{.data.<key>}' | base64 -d`.
