---
title: "Notes from Kubernetes Patterns (2nd edition)"
date: 2023-01-15T01:19:07+05:00
tags: [books, links, k8s, review]
description: 'Collection of courses, videos, books related to system design interview preparation. Constantly updated.'
---
[Website](https://k8spatterns.io/) of the book.

I am reading Early Preview in the begging of Jan 2023.

### Chapter 1

### Chapter 2
Kubernetes supports:
* Rolling deployment (aka `RollingUpdate`) - ensure no downtime by replacing one-by-one old containers with new versions
* Fixed deployment (aka `Recreate`) - stop old containers, launch new ones, wait. The biggest issue is downtime, while new version is creating.

K8S doesn't support out of the box complex deployment strategies, like canary or blue-green. For that purpose you have to use custom operators:
* [Flagger](https://flagger.app/) - integrates with many Ingress controllers and service meshes
* [Argo Rollouts](https://argoproj.github.io/rollouts/) - similar to Flagger
* [KNative]() - support traffic splitting, but not advanced as Flagger or Argo

All these projects are part of CNCF.

### Chapter 3
