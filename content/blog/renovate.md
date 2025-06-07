---
id: Renovate Intro
aliases: []
tags:
  - Automation
  - Dependency management
date: "2025-04-16"
title: Renovate Intro
description: "A brief introduction to Renovate"
keywords: ["DevOps", "Automation", "Dependency management"]
---

I’m a huge fan of automation—but only when I can stay in full control.
That principle guides a lot of how I manage my homelab, especially when it
comes to dependencies. I want things to be streamlined, but not blindly automatic.

Enter Renovate: a powerful tool that keeps an eye on your repositories
and automatically opens pull requests when upstream container images get updated.
It even creates a persistent “dependency dashboard” issue in your repo
to keep track of what’s monitored and what needs attention.
(Don’t panic—it’s not a bug, it’s a feature.)

## My Setup

I installed Renovate via the GitHub Marketplace, added a simple renovate.json
to the root of this [project](https://github.com/FedericoSerini/homelab), and marked all my relevant YAML files for monitoring.
It was plug-and-play for the most part.

One thing I had to tweak: I exclude my personal ghcr.io images from scanning.
Since they’re private and require authentication—and more importantly,
because I manage their release cycle myself—Renovate doesn't need to track them.

## Why I Trust It (With Safeguards)

Even though Renovate helps automate updates, I never blindly merge changes.
I always check release notes and changelogs before accepting a pull request.
Automation doesn’t replace judgment—it just frees me to focus that judgment
where it matters.

And since I manage everything with GitOps, if something ever breaks after a merge,
I can:

* Instantly roll back by reverting the Git commit.
* Dive into the Kubernetes logs to see what went wrong.
* Fix it without needing to SSH into anything manually.

This setup gives me the best of both worlds: speed and safety, automation and control.

And here’s a snippet of the YAML config I used:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "config:recommended"
  ],
  "ignoreDeps": ["ghcr.io/federicoserini/phos-metadata-service"],
  "kubernetes": {
    "fileMatch": [
      "\\.yaml$"
    ]
  }
}
```
