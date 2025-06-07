---
next: /docs/infrastructure/monitoring/
prev: /docs/infrastructure
title: Operators
weight: 1
---

## What’s Running Behind the Scenes

Here’s a quick look at some of the Kubernetes operators currently running in my homelab.
Most of them are installed and managed via HelmRelease,
because I like things declarative, repeatable, and clean.

This stack is constantly evolving, but here are a few of the key pieces
I’ve got in place right now:

* CloudNativePG – My go-to for running PostgreSQL natively on Kubernetes.
It plays well with backups and failovers, which keeps things tidy.
* ExternalDNS – Handles DNS updates automatically,
so I don’t have to. Feels like magic.
* Falco – A little security paranoia never hurts—Falco helps
monitor for unexpected behavior right at the kernel level.
* Keycloak – Centralized identity and access management. It’s how I
secure most of the apps in my homelab, including Phos.
* MariaDB – Lightweight and reliable, perfect for smaller services
that don’t need full-blown Postgres.
* MongoDB – My choice for flexible, document-based data—used in
a few internal tools like Phos.
* Synology CSI Storage – Hooks my NAS directly into the cluster.
Persistent storage without overthinking it.
* Kube Prometheus Stack – Metrics, alerts, dashboards—it’s
the observability core of my cluster.

This stack gives me a strong baseline for experimenting, learning,
and building without cutting corners on stability or security.
