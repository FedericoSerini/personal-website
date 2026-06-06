---
title: Claude Code Plugin
weight: 1
prev: /docs/ai/
---

The homelab ships a [Claude Code](https://claude.ai/code) plugin that turns the AI assistant
into a cluster-aware ops tool. Instead of copy-pasting `kubectl` commands manually,
I can ask Claude to check app status, tail logs, debug failures, or scaffold a new app—
and it already knows the cluster topology from `INFRA.md`.

The plugin is open source: [github.com/FedericoSerini/homelab-plugin](https://github.com/FedericoSerini/homelab-plugin)

## How It Works

At session start, the plugin loads `INFRA.md`—a compact, machine-readable file that describes
the entire cluster: nodes, apps, namespaces, database backends, ingress hosts, known traps.
Every slash command uses this context so it never has to guess which namespace an app lives in
or what image it's running.

## Installation

```shell
claude plugin install https://github.com/FedericoSerini/homelab-plugin
```

Then open Claude Code in the homelab repo and run `/hl-init` to scaffold the `INFRA.md` template.

## Commands

| Command | What it does |
|---|---|
| `/hl-status` | Full cluster health check — HelmReleases, Kustomizations, non-Running pods. Issues listed first. |
| `/hl-info <app>` | Static config + live pod status for a named app. Compares live image against INFRA.md. |
| `/hl-logs <app>` | Compressed pod logs. Strips Java stack traces to `Caused by:`, drops INFO noise. |
| `/hl-debug <app>` | Diagnostic sequence: HelmRelease conditions → pod status → logs → reasoning against known traps. |
| `/hl-fix` | Applies the right fix sequence for stalled HelmReleases, ConfigMap patches, StatefulSet restarts. |
| `/hl-rollback <app>` | Reverts the last git commit touching the app and pushes to `main` so Flux reconciles back. |
| `/hl-updates` | Lists open Renovate PRs — pending image and chart version bumps. |
| `/hl-docs <topic>` | Explains a cluster component or answers an infra question using INFRA.md + official docs. |
| `/homelab-new-app` | Full guided workflow to onboard a new app: tunnel creation, manifest scaffolding, SOPS secret encryption, Flux wiring. |

## INFRA.md

`INFRA.md` is the single source of truth the plugin reads at session start.
It lives at `.claude/INFRA.md` in the homelab repo and is kept up to date alongside
every infrastructure change. Format is intentionally compact — the plugin loads it
into context without burning tokens on verbose YAML.

```
CLUSTER: Talos v1.13.3 / k8s v1.36.1
APPS:
  bookstack  ns:bookstack  img:linuxserver/bookstack:26.03  port:80  db:MariaDB  host:bookstack.federicoserini.com
  ...
KNOWN TRAPS:
  Falco chart 9.0.0: daemon config must be nested under the `falco:` key — top-level is silently ignored
  ...
```

The `KNOWN TRAPS` section is especially useful: hard-won lessons from past incidents
are baked into the file so `/hl-debug` can reason against them without re-discovering
the same footguns.
