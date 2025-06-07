---
title: Phos
weight: 4
prev: /docs/apps/mealie/
next: /docs/apps/kubeproxy/
---

## Description

Phos is my own self-hosted video streaming platform. The name comes from
the Greek word phōs, meaning light—and since light is the essence of video,
the name just felt right. I tend to give my projects mythologically inspired
names—partly because I love the symbolism,
partly because I’m just a mythology nerd at heart.

## Why

I love a good technical challenge—and streaming video definitely qualifies.
Phos started as an experiment in learning and has grown into something
personal: a platform where I stream footage from my drone, and even videos of myself
explaining concepts as a way to improve my public speaking and tech storytelling.

It’s not built for public release (yet), but it’s been a fun ride pushing
my limits and playing with real-time media.

## Under the Hood

Phos uses HLS for streaming and FFmpeg, controlled at a low level via JNI,
for video transcoding. Metadata lives in MongoDB, search is powered by Solr,
and Debezium handles syncing between systems.

To keep everything secure—but still user-friendly—I’ve integrated Keycloak
for authentication and access control. Even though it’s just me using the app,
I like to treat it as if it were production-grade.

Most of the code is private right now—not because it’s secret, but because
I’m still not happy with the quality. It’s a work in progress, like most good things.
