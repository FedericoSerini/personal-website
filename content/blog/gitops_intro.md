---
id: GitOps Intro
aliases: []
tags:
  - GitOps
  - FluxCD
date: "2025-02-11"
title: GitOps Intro
description: "GitOps Intro"
keywords: ["DevOps", "Kubernetes", "GitOps", "FluxCD"]
---

## GitOps: An Evolution of IaC  

GitOps is a concept that enables developers to interact with CI/CD pipelines
or Kubernetes clusters in a **declarative** way—leveraging Git’s
powerful collaboration and versioning features.  

With GitOps, all environment configurations and Kubernetes application manifests
can be stored in a Git repository and automatically applied by a GitOps controller.
This approach is incredibly powerful because it allows for **fully automated workflows**,
near-instant **deployment of changes**, and easy **rollback**
using `git revert` if something goes wrong.  

At its core, GitOps can be seen as an **evolution of the Infrastructure
as Code (IaC) concept**.  


## Key Benefits of GitOps  

- **Faster and more frequent deployments**  
- **Simple and quick rollbacks**  
- **Full version history of infrastructure changes**  


## GitOps Controllers  

The two most widely used GitOps controllers are:  

- **ArgoCD**  
- **FluxCD**  

These tools monitor a Git repository for changes and automatically apply
updates to the infrastructure.  

### Why I Use FluxCD in My Homelab  

In my homelab, I use **Flux** because it’s **easier to install** and offers
a more **practical learning experience**. Unlike ArgoCD, Flux doesn’t
have an advanced UI, making it a great tool for getting hands-on
experience with GitOps fundamentals.  
