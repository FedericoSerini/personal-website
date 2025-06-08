---
id: Using SOPS Secrets with Age
aliases: []
tags:
  - GitOps
  - FluxCD
  - Secret Management
date: "2025-02-19"
title: Using SOPS Secrets with Age
description: "A brief introduction to SOPS secrets and how they fits in my homelab"
---

In my [Homelab](https://github.com/FedericoSerini/homelab), I needed a secure way to store secrets without exposing plain credentials in my GitOps repository. After evaluating three options:

1. Using a secret manager like [Hashicorp Vault](https://www.vaultproject.io/)
2. Using [AWS Secrets Manager](https://aws.amazon.com/secrets-manager/?nc1=h_ls)
3. Installing a tool directly on my Kubernetes cluster

I decided to go with the third option for now, with plans to switch to AWS Secrets Manager later when I feel ready.

According to the official [FluxCD](https://fluxcd.io/flux/guides/mozilla-sops/) documentation, two recommended tools for this approach are:

- [SOPS](https://github.com/getsops/sops) (Secrets Operations)
- [Age](https://github.com/FiloSottile/age) (a simple, modern encryption tool)

Below is my setup process.

---

## Installing SOPS and Age

To install **SOPS** and **Age** on my Linux cluster, I used Homebrew:

```bash
brew install sops age
```

Next, I generated a key pair (public & private) and exported the public key as an environment variable:

```bash
age-keygen -o age.agekey

cat age.agekey

export AGE_PUBLIC=<age.agekey public key>
```

---

## Encrypting a Generic Secret

As an example, I created a Kubernetes secret using `kubectl` and saved it to a file. The `--dry-run=client` flag ensures the resource is not applied directly to the cluster:

```bash
kubectl create secret generic <secret_name> \
  --from-literal=user=admin \
  --from-literal=password=\!Abc123 \
  --dry-run=client \
  -o yaml > secret.yaml
```

Then, I encrypted the secret file. The regex ensures that everything inside the `data` property is encrypted:

```bash
sops --age=$AGE_PUBLIC \
  --encrypt --encrypted-regex '^(data|stringData)$' --in-place secret.yaml
```

This produces an encrypted secret. You can see an example in my [Homelab repository](https://github.com/FedericoSerini/homelab/blob/main/apps/staging/linkding/linkding-secret.yaml).

---

## Configuring the Cluster & FluxCD

### 1. Creating the Age Secret in the Cluster

The **Age** secret must be manually created in the cluster:

```bash
cat age.agekey | \
kubectl create secret generic sops-age \
  --namespace=flux-system \
  --from-file=age.agekey=/dev/stdin
```

### 2. Defining Encryption Rules

In the `clusters/environment` directory of the GitOps repository, I created a `.sops.yaml` file to define encryption rules:

```yaml
creation_rules:
  - path_regex: .*.yaml
    encrypted_regex: ^(data|stringData)$
    age: <age.agekey public key>
```

### 3. Configuring Decryption in FluxCD

For any **Kustomization** resource that requires decryption, I added the following under the `spec` section:

```yaml
decryption:
  provider: sops
  secretRef:
    name: sops-age
```
