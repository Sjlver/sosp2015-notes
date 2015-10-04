The Rise of Cloud Computing Systems
===================================

Speaker: Jeff Dean
Sunday, 15:30

Multics had this vision of "utility computing". Give people computing power when
they need it.

After a while, PCs became cheap so that everybody could have one.

Over time, we've gone back to a centralized notion (shows pictures of Google
data centers). There are high-speed networks connecting many small components to
a bigger unit.

## How did we get here?

In the nineties, distributed systems were modest-scale (5 machines...)

High-performance computing had a focus on performance but not on
fault-tolerance.

Transactional systems and databases had a focus on fault-tolerance but less on
performance.

What caused the need for very large-scale systems?

- The web was the first very large data set.
  It grew to millions of pages. These needed indexing, searching, ...

One of the early projects was Berkeley NOW: computers get cheaper; can connect
them with a fast network to get really high computing power.

Jeff Dean started at DEC WRL (who built Altavista). Ultimately, he wanted to
join a small company, so he went to Google in 1999 :)

Early Google tenet: commodity PCs gave high perf per dollar. Commodity
components were even better. So Google built these racks out of Lego and cork :)

A typical cluster sees, in its first year

- 1 rewiring
- 20 rack failures
- ...

=> Reliability must come from software, not hardware.

## Common theme: systems need to self-manage and self-repair.

### 1. A distributed file system

"Gee, I need to store stuff."

- NFS (Xerox Alto, 1973)
- AFS
- xFS
- Google File System

In GFS, a centralized master manages all the metadata (surprise, scales
reasonably well).

The master managers metadata, but apart from that the clients communicate
directly with the disk machines.

Chunks of 64MB, each replicated three-way. => Can just replace failed disks, and
the system will self-repair.

### 2. Process data in parallel

Once you can store so much data, you want to be parallel computation on it.

One important building block: the scheduler.

Multiple approaches here:

- task = VM
- task = Container (a bit more lightweight, at the process level)

Scheduler needs to place these tasks on physical machines, while satisfying task
requirements and handling machine failures.

Many systems:
- Borg
- Apache Mesos
- Kubernetes

Tension between multiplexing resources and performance isolation. Sharing leads
to unpredictable performance. In particular, it's very important to control tail
latency.

### 3. Higher-level programming frameworks

"It's actually nicer to not have to think about tasks as much."

MapReduce: simple map and reduce abstraction. Hides messy details on how this is
mapped to cluster machines. Can still handle locality. Makes it rather easy for
people to write a variety of large-scale computations.

Many successors:

- Dryad
- Sawzall, PIG, DryadLinq, Flume
- Pregel
- Spark

At the same time, many applications now needed to update structured data =>
MapReduce alone wasn't such a good fit anymore.

=> Distributed semi-structured storage systems:

- BigTable: higher-level storage with rows, columns, timestamps. Built on top of
  GFS. No cross-row consistency guarantees. State was managed in small pieces
  called tablets, which made recovery fast.
- Dynamo (Amazon, 2007): app-assisted conflict resolution
- Spanner (Google, OSDI 2012): Wide-area distribution. Supports both strong and
  weak consistency.

Successful design patterns here: *Give each machine hundreds or thousands of
units of work or state*.

### 4. The public cloud

Such systems are now becoming available to everybody.

- Amazon: Queue API in 2004, EC2 in 2006.
- Google: AppEngine in 2005, other services later
- Microsoft: Azure in 2008

Shift towards such services is increasing. Also, APIs are getting more complete,
so you can do many more things in the cloud than before.

## What's next?

Abstractions for interactive services with 100s of subsystems

- Complex configuration
- Many opportunities for automation

Systems that handle greater heterogeneity

- E.g., automatically split computation between mobile device and cloud
