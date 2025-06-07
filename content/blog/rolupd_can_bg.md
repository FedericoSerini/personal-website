---
id: Rolling Updates vs Canary vs Blue/Green deployment strategies
aliases: []
tags:
  - deployment
  - rolling updates
  - canary deployment
  - blue and green deployment
  - strategies
date: "2025-06-07"
title: Rolling Updates vs Canary vs Blue/Green deployment strategies
description: "Key differences between most common deployment strategies"
keywords: ["DevOps", "deployment strategies", "rolling updates", "canary deployment", "blue and green deployment"]
---

In Kubernetes—and DevOps in general—how you deploy can be just as
important as what you deploy. A solid deployment strategy helps
reduce risk, improve availability, and roll out changes confidently.

This post breaks down key deployment strategies, with practical
examples and real-world tradeoffs.

## Rolling Update (Kubernetes Default)

* Replaces old pods with new ones incrementally.
* Controlled via maxUnavailable and maxSurge.
* No traffic routing needed—Kubernetes handles everything.

```yaml

strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 1
    maxSurge: 1
  ```

>Pros: Simple, efficient, minimal resource cost  
Cons: No version isolation—bugs in new pods can affect users immediately

## Blue/Green Deployment

* Two full environments: Blue (live), Green (staging/testing).
* New version is deployed to Green, tested, and traffic is switched over when ready.
* Rollback is instant—just point traffic back to Blue.

> Pros: Safe, instant rollback  
Cons: Requires extra infra and routing control (Ingress or Load Balancer)

### How to implement

* You deploy two versions of your app: Blue (current live) and Green (new candidate).
* Once Green is validated, you switch traffic to it using your ingress or load balancer.
* Rollback = switch back to Blue.

### When to use

* Mission-critical apps where downtime is unacceptable
* You want fast rollback without redeploying
* You have capacity for two environments

## Canary Deployment

* Gradual rollout to a subset of users.
* Observability is key—monitor metrics, logs, errors before scaling up.

Example:

```text
5 total replicas
→ 1 pod runs v2 (canary)
→ 4 pods run v1 (stable)
```

> Pros: Real-world testing, minimal risk  
Cons: Needs advanced tooling (Istio, Flagger, Argo Rollouts), proper metrics & alerting

### How to implement

* Deploy new version gradually (e.g., 10% of traffic → 25% → 50%)
* Monitor errors, metrics, latency
* Promote if healthy, rollback if not

### When to Use

* You want to test new features in production
* Risk needs to be managed progressively
* You have observability in place (Prometheus, Grafana, etc.)

## Final thoughs

Choosing the right deployment strategy depends on:

* Risk tolerance
* App criticality
* Infrastructure budget
* Observability maturity

In my homelab, I use Rolling Updates, but I’m actively exploring
Canary + GitOps + Flagger for more controlled experiments.
