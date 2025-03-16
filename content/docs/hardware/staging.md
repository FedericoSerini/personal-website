---
title: Staging
weigth: 1
prev: /docs/hardware/
next: /docs/startup/
---

The staging environment is meant for testing, but for now, it is temporarily acting as production.

In this initial phase, I have chosen Linux Ubuntu Server as the operating system. However, in the future, I plan to switch to a Kubernetes-based OS, specifically Talos Linux.

Currently, the system has a single point of failure since the same hardware serves as both the control plane and worker node. While hardware can be replaced, data cannot, which is why a minimal disaster recovery policy is in place. This involves storing data in AWS S3 with isolated users, following the Principle of [Least privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## Nodes

|#|Model|Name|
|--|--|--|
|0|Lenovo ThinkCentre M900 Tiny|Maestrale|
