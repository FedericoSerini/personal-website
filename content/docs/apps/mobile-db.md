---
title: Mobile DB
weight: 6
prev: /docs/apps/kubeproxy/
next: /docs/apps/moneybud/
---

## Description

Mobile DB is a self-hosted sync backend for mobile apps. It uses
[crsqlite](https://github.com/vlcn-io/cr-sqlite)—a CRDT extension for SQLite—to enable
conflict-free replication between clients. Changes made offline merge cleanly when
connectivity is restored, without manual conflict resolution.

## Why

Most sync solutions either lock you into a proprietary backend or require you to
run something heavy like Firebase or a full-blown PostgreSQL setup with custom
sync logic on top. crsqlite offers a different path: keep SQLite on the client
(lightweight, zero-latency reads) and let the server handle replication
using conflict-free data structures.

It's the backbone for my mobile projects—any app that needs offline-first data
with reliable sync connects through here.

## Under the Hood

- **Storage**: SQLite with the crsqlite extension, persisted to a PVC
- **Protocol**: HTTPS on port 8443
- **Auth**: SOPS-encrypted secret (`mobile-db-secret`) injected at runtime
- **Image**: private, hosted on GitHub Container Registry
