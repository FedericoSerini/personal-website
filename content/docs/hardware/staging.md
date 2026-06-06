---
title: Staging
weigth: 1
prev: /docs/hardware/
next: /docs/startup/
---

The staging environment is a single-node cluster that currently doubles as production.
It runs [Talos Linux](https://www.talos.dev/)—a minimal, immutable OS purpose-built for Kubernetes,
with no SSH daemon and no shell. Everything is managed via `talosctl` and the GitOps pipeline.

## Nodes

|#|Model|Name|Role|
|--|--|--|--|
|0|Lenovo ThinkCentre M900 Tiny|Maestrale|control-plane + worker|

## OS: Talos Linux v1.13.3

Talos was chosen over a general-purpose Linux distribution for a few key reasons:

- **Immutable**: the OS is read-only—no drift, no manual changes, no forgotten one-liners.
- **No shell, no SSH**: the attack surface is dramatically smaller. All node config is applied
  declaratively via `talosctl`.
- **Kubernetes-native**: Talos boots straight into Kubernetes. No package managers,
  no systemd services to babysit.

The node runs **k8s v1.36.1** with **containerd 2.2.4**.

### Extensions

The Talos image is built with a custom schematic that bundles:

- `iscsi-tools` — required for the Synology CSI driver to provision iSCSI volumes
- `tailscale` — VPN mesh access to the cluster from anywhere

### Custom sysctls

One non-default sysctl is required for Falco's eBPF driver:

```yaml
kernel.perf_event_paranoid: "1"
```

Talos defaults this to `3`, which blocks `modern_ebpf` tracepoint attachment.
The patch is applied via `talosctl patch machineconfig` and lives in the repo alongside the node config.

## Disaster Recovery

A single node means a single point of failure—so data durability is handled at the application layer:

- **MariaDB**: monthly automated backup job → AWS S3
- **PostgreSQL (CNPG)**: on-demand backup via `Backup` CRD → AWS S3
- All S3 access uses isolated IAM users with minimal permissions ([Least Privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege))

Hardware can be replaced; data cannot—so backups are non-negotiable even at this scale.
