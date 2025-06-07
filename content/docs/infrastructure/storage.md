---
title: Storage
weight: 4
prev: /docs/infrastructure/databases/
next: /docs/security
---

Let’s talk storage—because even the most elegant cluster needs a solid
place to keep its stuff. Here's how I’ve set things up in my homelab:

* I’m using a Synology DS224+ as the
primary storage backend. It’s reliable,
compact, and fits nicely into my home
setup without sounding like a jet engine.

* The system currently runs with 8TB of
storage in RAID 1, giving me some peace
of mind with redundancy.

* Storage is connected via iSCSI, with
automatic provisioning handled by the
Synology CSI driver. This means volumes
are created and managed dynamically,
without manual tinkering every time a new
service comes online.

* As for the filesystem, I went with
BTRFS. Sure, EXT4 is more portable, but I
prefer BTRFS for its native error
checking and snapshot capabilities. It's
like having an extra layer of sanity
built into the foundation.

This setup gives me a good balance of performance, reliability,
and automation—plus, it’s been rock solid so far.
