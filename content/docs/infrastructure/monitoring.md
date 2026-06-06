---
next: /docs/infrastructure/databases/
prev: /docs/infrastructure/operators/
title: Monitoring
weight: 2
---

Observability runs on the [kube-prometheus-stack](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack)
operator—a single HelmRelease that deploys Prometheus, Grafana, Alertmanager, and a
full set of Kubernetes recording rules and dashboards.

## Components

**Prometheus** scrapes metrics from all cluster components via `ServiceMonitor` and `PodMonitor` CRDs.
Adding a new scrape target is as simple as creating a `ServiceMonitor` in the right namespace.

**Grafana** provides dashboards for cluster health, workload performance, and custom app metrics.
Dashboards can be provisioned automatically via ConfigMaps labeled `grafana_dashboard: "1"`.
The Grafana sidecar picks them up without a pod restart.

**Alertmanager** handles alert routing. No external notification target is configured yet—
mostly used to understand alert evaluation at this point.

## Security Monitoring Metrics

Falco Talon exposes Prometheus metrics for every action it fires:

| Metric | What it tracks |
|---|---|
| `falcosecurity_falco_talon_matches_total` | Falco events matched by Talon rules |
| `falcosecurity_falco_talon_actions_total` | Actions fired (with `status` label for success/fail) |
| `falcosecurity_falco_talon_notifications_total` | Notifications sent |

These are scraped automatically via a `ServiceMonitor` on the Falco Talon service,
and a bundled Grafana dashboard surfaces them under **Dashboards → falco-talon**.

## Adding Custom Dashboards

Drop a ConfigMap into the `monitoring` namespace with the `grafana_dashboard: "1"` label
and the JSON dashboard in a data key:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-dashboard
  namespace: monitoring
  labels:
    grafana_dashboard: "1"
data:
  my-dashboard.json: |
    { ... }
```

Grafana's sidecar will detect and load it automatically.
