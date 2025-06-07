---
title: Kubeproxy
weight: 5
prev: /docs/apps/phos/
next: /docs/infrastructure/
---

## 🚧 Under Construction

### Highlights

A lightweight app to monitor the status of resources inside the Kubernetes cluster, is a read only application.

### Why

I’m pretty cautious when it comes to exposing anything from my homelab to the outside world—especially my monitoring stack or Kubernetes cluster. Even if this app comes with authentication in place (shoutout to OIDC), I’d rather not take unnecessary risks.

That said, there are times when I’m far from home—maybe out hiking or just on a coffee run—and I get an idea I want to quickly validate or I’m just curious to check on the cluster’s health. To solve this, I’ve been building a read-only app that gives me just enough visibility to feel connected without opening up any serious attack surface. No controls, no write access—just basic stats, logs, and state info. It’s like a safe little window into my homelab, and it’s already saved me a few “wait, is it running?” moments.
