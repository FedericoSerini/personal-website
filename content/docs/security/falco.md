---
title: Falco
weight: 1
prev: /docs/security
next: /docs/security/secrets
---

[Falco](https://falco.org/) is a cloud-native runtime security tool that monitors kernel-level
events and fires alerts when something unexpected happens—a process doing something it shouldn't,
a container spawning a shell, a package manager running inside a pod.

## Setup

Falco runs as a HelmRelease (`falco@9.0.0`) in the `falco` namespace, using the `modern_ebpf` driver.
This attaches directly to the kernel via eBPF tracepoints—no kernel module needed,
which fits well with Talos's immutable approach.

One prerequisite on Talos: the kernel sysctl `kernel.perf_event_paranoid` must be set to `1`
(Talos defaults to `3`). This is patched into the node machine config and committed to the repo.

## Automated Response: Falco Talon

Monitoring is only half the story. When Falco detects a critical event, [Falco Talon](https://falco-talon.org/)
takes action automatically—no manual intervention needed.

Talon (`falco-talon@0.4.0`) receives Falco events via HTTP output and executes a configured response.

### Active Rule

```yaml
- action: kubernetes:terminate-pod
  match:
    priority: CRITICAL
    output_fields:
      k8s.ns.name: bookstack
```

Any `CRITICAL`-priority Falco event originating from the `bookstack` namespace triggers
an immediate pod termination. The pod is gone in under a second.

The rule that currently fires this is **Block Package Managers Execution**—if anything
inside the bookstack pod runs `curl`, `wget`, `apt`, `apk`, or similar, the pod is terminated.

### Observability

Talon exposes Prometheus metrics scraped automatically via a `ServiceMonitor`.
A bundled Grafana dashboard surfaces termination counts and action success/failure rates.
See the [Monitoring](/docs/infrastructure/monitoring) page for details.
