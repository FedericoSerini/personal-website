---
title: Keycloak
weight: 3
prev: /docs/security/secrets
next: /docs/ai/
---

[Keycloak](https://www.keycloak.org/) is the identity provider for the homelab.
It handles authentication and authorization for apps that support OIDC,
so I don't have to implement login flows in every service I build.

## Setup

Keycloak runs as a HelmRelease using the [codecentric/keycloakx](https://github.com/codecentric/helm-charts/tree/master/charts/keycloakx)
chart (`keycloakx@7.2.0`) in the `keycloak` namespace.

It's exposed at [gatekeeper.federicoserini.com](https://gatekeeper.federicoserini.com)
via a Cloudflare Tunnel, like all other homelab services.

## Database

Keycloak uses a dedicated **PostgreSQL 17** cluster provisioned by CloudNativePG (`pg-keycloak`
in the `keycloak-pg` namespace). Connection details are injected at runtime from
a SOPS-encrypted secret.

## OIDC Integrations

| App | Auth flow |
|---|---|
| Phos | OIDC — all access controlled via Keycloak realms |
| KubeProxy | OIDC — read-only cluster viewer, auth-gated |

Adding a new OIDC client is done entirely in the Keycloak admin console—
no cluster changes required unless the app needs a new secret injected.

## Notes

Keycloak runs in production mode, which requires the `KC_HOSTNAME` environment variable
to be set. Without it, the server refuses to start. The value is set to the public hostname
(`gatekeeper.federicoserini.com`) in the HelmRelease values.
