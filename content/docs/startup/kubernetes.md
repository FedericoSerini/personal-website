---
title: Kubernetes
weight: 1
prev: /docs/startup/
next: /docs/startup/fluxcd/
---

The cluster runs on [Talos Linux](https://www.talos.dev/)—an immutable, API-driven OS
with no SSH and no shell, designed exclusively for Kubernetes.

## Building the Talos Image

Rather than using a stock Talos image, I build a custom one via the
[Talos Image Factory](https://factory.talos.dev/) with the extensions I need:

- `iscsi-tools` — Synology CSI driver dependency
- `tailscale` — VPN access to the cluster

The result is a schematic ID that pins the exact image used to provision the node.
Reproducible and auditable.

## Bootstrapping the Node

Talos is bootstrapped by applying a machine config to the node over the network.
No installer ISO needed once the image is in place.

```shell
talosctl apply-config --insecure --nodes <node-ip> --file controlplane.yaml
```

After applying config, bootstrap the Kubernetes control plane:

```shell
talosctl bootstrap --nodes <node-ip> --talosconfig=./talosconfig
```

Retrieve the kubeconfig:

```shell
talosctl kubeconfig --nodes <node-ip> --talosconfig=./talosconfig
```

## Patching Machine Config

Since there's no shell on Talos, all node-level changes go through `talosctl patch machineconfig`.
For example, the Falco eBPF driver requires a non-default sysctl:

```shell
talosctl patch machineconfig \
  --nodes <node-ip> \
  --patch '[{"op":"add","path":"/machine/sysctls","value":{"kernel.perf_event_paranoid":"1"}}]'
```

## Exporting Kubeconfig

```shell
talosctl kubeconfig ~/.kube/config \
  --nodes <node-ip> \
  --talosconfig ~/talos-config/talosconfig
```

From here, standard `kubectl` commands work as expected against the cluster.
