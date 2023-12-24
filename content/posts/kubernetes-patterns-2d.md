---
draft: true
title: "Notes from Kubernetes Patterns (2nd edition)"
date: 2023-01-15T01:19:07+05:00
tags: [books, links, k8s, review]
description: 'Collection of courses, videos, books related to system design interview preparation. Constantly updated.'
---
[Website](https://k8spatterns.io/) of the book.

I am reading Early Preview in the begging of Jan 2023.

## 1. Predictable demands

...

## 2. Declarative deployment
Kubernetes supports:
* Rolling deployment (aka `RollingUpdate`) - ensure no downtime by replacing one-by-one old containers with new versions
* Fixed deployment (aka `Recreate`) - stop old containers, launch new ones, wait. The biggest issue is downtime, while new version is creating.

K8S doesn't support out of the box complex deployment strategies, like canary or blue-green. For that purpose you have to use custom operators:
* [Flagger](https://flagger.app/) - integrates with many Ingress controllers and service meshes
* [Argo Rollouts](https://argoproj.github.io/rollouts/) - similar to Flagger
* [KNative]() - support traffic splitting, but not advanced as Flagger or Argo

All these projects are part of CNCF.

## 3. Health Probe
Bug-free code is hard, especially in distributed systems, that's why in most cases it's better to concentrate on recovery.

Kubernetes supports:
* **Process health check** - if container process is not running, the container restarted. It is ideal, if application can detect failure and shutdown itself.
* **Liveness probe** - restarts container if probe failed. Probe considered successful if:
  * HTTP call - any response with status code 200-399
  * TCP call - open TCP connection
  * Exec - executes command and expects 0 exit code
  * [gRPC](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#define-a-grpc-liveness-probe) - enable `GRPCContainerProbe` feature gate and implement [gRPC health checking protocol](https://github.com/grpc/grpc/blob/master/doc/health-checking.md)
* **Readiness probe** - removes container from service if probe failed. POD is ready, when all containers are ready.
  * **[Readiness gates](https://martinheinz.dev/blog/63)** - requires external service, who will flip readiness flag.
* **Startup probe** - similar to Liveness probe, however long startup might affect liveness probe - for that purpose a special startup probe was created. Liveness/readiness checks will work right after startup probe success.

## 4. Managed lifecycle

When POD should be terminated Kubernetes sends **SIGTERM** and after 30 seconds (or `.spec.terminationGracePeriodSeconds`) **SIGKILL**. Both signals might be not enough, that's why Kubernetes supports additional lifecycle hooks: `postStart` and `preDestroy`. For example, following `postStart` waits 30 seconds and when writes to file:

```yaml
containers:
  - image: k8spatterns/random-generator:1.0
    name: random-generator
    lifecycle:
      postStart:
        exec:
        command:
          - sh
          - -c
          - sleep 30 && echo "Wake up!" > /tmp/postStart_done
```
`postStart` runs simultaneously with container initialization (or before container has started!), and it is blocking: container remains in _Waiting_ state and POD in _Pending_ until completion, that's why sometimes `postStart` used as delay for main process. `postStart` might be used as precondition: if hook process returned non-zero result, main process will be killed.

`preStop` has similar syntax and semantics however doesn't prevent POD from deletion.

## 