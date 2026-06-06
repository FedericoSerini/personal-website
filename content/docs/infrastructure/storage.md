---
title: Storage
weight: 4
prev: /docs/infrastructure/databases/
next: /docs/security
---

Even the most elegant cluster needs a solid place to keep its stuff.
Here's how storage is set up in my homelab:

## Hardware

A **Synology DS224+** serves as the primary storage backend.
It's reliable, compact, and fits into a home setup without sounding like a jet engine.
The system runs **two 4TB disks in RAID 1**, so a single-disk failure doesn't mean data loss.

## Provisioning

Storage is connected via **iSCSI**, with dynamic volume provisioning handled by the
[Synology CSI driver](https://github.com/SynologyOpenSource/synology-csi).
The `synology-iscsi-storage` StorageClass is set as the cluster default—so any
`PersistentVolumeClaim` that doesn't specify a StorageClass gets a Synology iSCSI volume automatically.
No manual tinkering every time a new service comes online.

## Filesystem

Volumes are formatted as **ext4**.

Talos Linux doesn't ship the btrfs kernel module, so BTRFS isn't an option without
custom kernel extensions. Ext4 is a reliable, well-supported choice that works
out of the box with the Synology CSI driver on Talos.

## Retention Policy

PersistentVolumes use the `Retain` reclaim policy. This means that deleting a PVC
does not automatically delete the underlying iSCSI volume on the NAS—useful
insurance against accidental data loss during Flux reconciliation.
