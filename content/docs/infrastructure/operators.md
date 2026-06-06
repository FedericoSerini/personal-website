---
next: /docs/infrastructure/monitoring/
prev: /docs/infrastructure
title: Operators
weight: 1
---

## What's Running Behind the Scenes

All operators are managed as HelmReleases via FluxCD—declarative, versioned, and reproducible.
Here's the current stack:

| Operator | Version | Purpose |
|---|---|---|
| [CloudNativePG](https://cloudnative-pg.io/) | 0.23.1 | PostgreSQL clusters with built-in backup and failover |
| [MariaDB Operator](https://github.com/mariadb-operator/mariadb-operator) | 0.28.1 | MariaDB instances and scheduled backup jobs |
| [MongoDB Community Operator](https://github.com/mongodb/mongodb-kubernetes-operator) | 0.13.0 | MongoDB replica sets for document-based workloads |
| [ExternalDNS](https://github.com/kubernetes-sigs/external-dns) | 1.15.0 | Automatic DNS record management |
| [Falco](https://falco.org/) | 9.0.0 | Kernel-level runtime security monitoring |
| [Falco Talon](https://falco-talon.org/) | 0.4.0 | Automated response to Falco events (e.g. pod termination) |
| [Keycloak](https://www.keycloak.org/) | 7.2.0 | Centralized identity and access management (OIDC) |
| [Synology CSI Driver](https://github.com/SynologyOpenSource/synology-csi) | — | Dynamic iSCSI volume provisioning from the NAS |
| [kube-prometheus-stack](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack) | 68.5.0 | Prometheus, Grafana, and alerting rules |

This stack is constantly evolving, but it gives a solid baseline for running
production-grade workloads at home—observability, security, storage, and identity all covered.
