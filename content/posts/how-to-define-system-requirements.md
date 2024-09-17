---
title: "How to define system requirements?"
date: 2023-12-17T17:19:07+05:00
tags: [ system design ]
description: 'Functional and non-functional requirements definition'
---

When we design a system, we need to make sure that our design address requirements. There are two type of requirements:

1. **Functional** - define the system behaviour: what a system supposed to do.\
   For example, a functional requirement for a messaging system may look like this:
   the system must allow applications to exchange messages

2. **Non-functional** - define qualities of a system: how a system is supposed to be.\
   For example, a non-functional requirements for a messaging system may be:
   the system should be scalable, highly available and fast

During an interview functional requirements are often stated ambiguously by the interviewer and non-functional
requirements might not be mentioned at all. There is a reason for this: the whole point of interviewers is to collect as
many data points about the candidate as possible by stating a problem in the generic form the interviewer starts to
collect these data points. Interviewer wants to see how you deal with ambiguity, whether we can identify key parts of
the system and define the scope of the problem. The interviewer needs all this to understand how we will approach
solving problems in real life when hired. Another important reason for clarifying requirements, especially
non-functional, is: system design interview problems are usually open-ended as they may have more solutions than problem
asked. There are many concepts, patterns, technologies that can be applied, and they all have trade-offs. When defined
non-functional requirements help us to navigate these trade-offs, they guide us. Let me give you an example.
Earlier we mentioned three possible non-functional requirements for a messaging system: scalable, highly available and
fast. It might be surprising, but these four words already can give us a lot of information. Let's think through them
and start with scalability requirement.

**Scalability**

* _Do we need to scale for writes or reads?_\
  Probably both since every message in the system should be written and then read at some later point.
  To scale the system for writes I need to partition messages and store them in multiple queues instead of single queue.
* _Among different partition strategies, which one should we choose?_\
  The hash strategy should be OK.
* _Where we can store messages quickly?_\
  It can be memory, where we can use bounded queue or disk.
  If it is the disk, we can use an append-only log or embedded database.
* _If database - should I peek a B-tree or LSM-tree?_\
  Most likely LSM-tree-based database, since these types of databases are faster for writes.
  As for scaling reads, partitioning will help here as well, since we will have a consumer for partition.
* _OK, should we choose push or pull for reading messages?_\
  If we go with pull,
  we should make sure that the system supports long-polling in order to decrease the number of read requests.

**High availability**

To achieve high-availability, we need to replicate messages.

* _Should we choose leader-based or leader-less replication?_\
  Most likely leader-based.
  However, in this case we need to solve a leader election problem.
  That should be easy, since we can use coordination service or use database which guarantees strong consistency.
  In order to make the system more reliable, we need to implement some protection mechanism.
  For example,
  load shedding or rate-limiting or
  even [shuffle sharding](https://aws.amazon.com/builders-library/workload-isolation-using-shuffle-sharding/).
* _Should we use a reverse proxy to implement all the functionality?_\
  Probably.
  If we go this route, we will simplify client-side logic for both message producers and consumers,
  since reverse-proxy will take care of partition discovery and request routing.
  
In order to make our messaging system fast, we should consider batching and compression of messages.
  If my messaging systems will be accessed by trusted clients and trusted environments,
  we can use TCP instead of HTTP for service communication.

Knowledge of these concepts not only serves you well during the system design interviews, but also in day-to-day work.
It will help you to:

* define functional requirements and scope of the work
* define non-functional requirements

Intrigued? Let's deep dive.

### Materials

1. [System Design for Interviews and Beyond](https://systemdesignthinking.thinkific.com/courses/system-design-for-interviews-and-beyond)
   course by Mikhail Smarshchok
2. 