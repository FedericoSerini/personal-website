---
id: The 12-Factor App Methodology
aliases: []
tags:
  - methodology
  - applications
  - software development
date: "2025-05-02"
title: The 12-Factor App Methodology
description: "A brief introduction to the 12-Factor app methodology"
keywords: ["DevOps", "software development", "12 Factor app", "methodology"]
--- 

In my Devops journey I have faced an interisting manifest about modern software
development applications. The [12-Factor App](https://12factor.net/) methodology
provides a set of best practices for building applications suited for
cloud-native environments. Even if most of them seems "trivial",
the adoption of this mindset is not so easy at all.

## I. Codebase  

Maintain a single, centralized codebase per application, stored in version control.
Avoid mixing multiple projects—each app should have its own repository and clearly
defined boundaries.

## II. Dependencies  

Declare all dependencies explicitly in a
single file (e.g., `requirements.txt` in Python)
and isolate them using environments like Docker or virtualenv.
This reduces build issues and ensures consistent behavior across environments.

## III. Config  

Store all configuration—such as credentials, URLs, and feature
flags—outside the code,typically in environment variables.
Sensitive data should be encrypted or stored in secure vaults.

## IV. Backing Services  

Treat backing services (databases, queues, caching layers) as attached resources.
Applications should never assume services run on `localhost`,
but instead use environment-driven configuration for flexibility.

## V. Build, Release, Run  

Separate the application lifecycle into three stages:

- **Build**: Compile and package the app.
- **Release**: Combine the build with configuration.
- **Run**: Execute the app in its target environment.

## VI. Processes  

Apps should be stateless and share nothing between instances. Any necessary state
should be stored in external systems like databases or caches.
Sticky sessions are discouraged as they violate this principle.

## VII. Port Binding  

Applications should expose services via a port and be self-contained.
This means they do not rely on an external web server like Apache or
Nginx to serve content.

## VIII. Concurrency  

Design apps to scale horizontally by running multiple stateless processes.
This makes it easier to handle increased loads and improves fault tolerance.

## IX. Disposability  

Apps must start quickly and shut down gracefully.
They should handle termination signals (e.g., `SIGTERM`)
to complete ongoing work before exiting, ensuring clean resource usage.

## X. Dev/Prod Parity  

Keep development, staging, and production environments as similar as possible.
This minimizes surprises when deploying and encourages tight feedback
loops between devs and ops.

## XI. Logs  

Treat logs as event streams. Instead of writing to files,
write logs to `stdout`/`stderr` so they can be captured by
log aggregators like Fluentd or ELK, ensuring visibility
even if instances crash or scale down.

## XII. Admin Processes  

Run one-off admin tasks (like database migrations) as standalone processes,
separate from the app’s main process.
This keeps the main app focused and prevents hidden dependencies.

---

**Conclusion**  
Adopting the 12-Factor methodology promotes consistency, scalability, and resilience.
Whether you're building microservices or monoliths,
these principles provide a strong foundation for modern app development.
