---
id: Falco
aliases: []
tags:
  - security
  - falco
date: "2025-03-19"
title: Falco Intro
description: "A brief introduction to Falco"
keywords: ["DevOps", "DevSecOps", "Security", "falco"]
---

## What is Falco

Falco is often described as the runtime security tool for Kubernetes—and for good reason.
It watches what’s happening inside your containers and raises alerts when something
sketchy goes down, like shell access inside a pod or a file write to a sensitive path.

In short: Falco is your cluster's bouncer. And in a homelab, where things are
always changing and not every container is built with perfect security in mind,
having runtime visibility is incredibly valuable.

## How I Integrated Falco into My Homelab

You can check out my actual deployment setup here:
[falco-helmrelease.yaml](https://github.com/FedericoSerini/homelab/blob/main/infrastructure/controllers/base/falco/falco-helmrelease.yaml)

I’m deploying Falco using Helm and Flux, and most of the configuration is kept
clean—except for one key customization: I needed to create a custom Falco rule
to avoid a potential exploit from my BookStack deployment.

BookStack is an application that, unfortunately, starts its pods as root.
Since I don’t directly control the release, I couldn’t just fix that upstream
or tweak the container image so I wrote a custom rule exception to alert if
a strange behavior happens inside the BookStack container.

## Falco Rule Overrides

Here’s a the configuration I had to make directly into the Helm release:

```yaml
customRules:
      custom-rules.yaml: |-
        - list: forbidden_cmd
          items: [apk, curl, wget]
        - list: kube_namespaces
          items: [bookstack]
        - macro: checked_namespaces
          condition: k8smeta.ns.name in (kube_namespaces)
        - macro: forbidden_procs
          condition: proc.name in (forbidden_cmd)
        - rule: Block Package Managers Execution
          desc: Detect and alert when apk, curl, or wget is executed inside the container
          condition: >
            (spawned_process 
            and container 
            and forbidden_procs
            and checked_namespaces)
            or (spawned_process
            and container 
            and shell_procs
            and proc.tty != 0
            and container_entrypoint
            and not user_expected_terminal_shell_in_container_conditions
            and checked_namespaces)
          output: "Package manager execution detected in container (command=%proc.cmdline namespace=%k8smeta.ns.name user=%user.name pod_uid=%k8smeta.pod.uid, pod_name=%k8smeta.pod.name)"
          priority: CRITICAL
          tags: [security, container]
```

## Sneaky problem

While setting this up, I hit a sneaky issue: Falco wasn't attaching useful
metadata to the events—like the namespace name.

After a bit of trial, error, and... let’s say "exploratory debugging",
I realized that I was missing this in the config:

```yaml
falco:
  jsonOutput: true
  logLevel: info
  kubernetes:
    enabled: true
```

That kubernetes.enabled: true flag is critical for Falco to use the MetaCollector
to pull Kubernetes context like namespace and labels. Surprisingly,
this wasn’t well-documented—at least not prominently—so I’m flagging
it here for anyone else who hits the same wall.

## Future Cleanup: Externalize the Rules

Right now, my custom rules are embedded directly in the HelmRelease manifest.
It works, but it’s not ideal. As the setup grows, I want to:

* Move custom Falco rules to an external ConfigMap or separate file in Git.
* Mount them into the Falco container via values.yaml or Helm extraVolumes.
* Let the HelmRelease focus purely on deployment, while the security rules are
versioned and maintained independently.

This will improve reusability, visibility, and separation of concerns in the GitOps model.

Also I plan to integrate Falco Talon to take things a step further. Instead of just alerting,
Falco Talon can trigger automated responses—like deleting a suspicious pod the moment
it behaves unexpectedly. In a tightly controlled homelab, this kind of response is practical
and adds an extra layer of defense. It’s DevSecOps with teeth.

## Final Thoughts

Setting up Falco in my homelab has been a fun deep dive into runtime security
and practical DevSecOps. It’s not just a checkbox—it’s a signal layer that
helps you trust what’s actually happening in your cluster.

If you're self-hosting apps like BookStack (or anything else you don’t fully control),
tools like Falco become even more valuable—but only if you tailor them smartly,
rather than turning them off when things get noisy.
