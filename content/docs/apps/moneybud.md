---
title: Moneybud
weight: 7
prev: /docs/apps/mobile-db/
next: /docs/infrastructure/
---

## Description

Moneybud is a personal budget tracker—a web app for monitoring income, expenses,
and budgets over time. It's built to be simple and opinionated: no feature bloat,
just a clean view of where the money goes.

## Why

Every budgeting app I tried was either too complex, too opinionated about categories,
or required handing over financial data to a third party.
Building my own meant I could design exactly the workflow I wanted,
keep the data entirely in my own infrastructure, and wire it up to other services
in the homelab down the line.

It also gave me a good excuse to experiment with Keycloak's OIDC flow in a
frontend app—`VITE_KEYCLOAK_URL` is baked at build time, and auth is handled
before any data is ever loaded.

## Under the Hood

- **Backend**: connects to [Mobile DB](/docs/apps/mobile-db) for data persistence and sync
- **Auth**: Keycloak OIDC — Keycloak URL baked in at build time via `VITE_KEYCLOAK_URL`
- **Image**: private, hosted on GitHub Container Registry
- **Port**: 3001
