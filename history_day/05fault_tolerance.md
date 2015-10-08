Fault Tolerance
===============

Speaker: Ken Birman
Sunday, 11:30

Many seminal concepts:

- Process pairs, primary and backups
- 2PC and 3PC, Quorums
- Atomic transactions
- Byzantine agreement
- Gossip protocols
- Paxos, ZooKeeper, ...

Sketch:

---

Transactions   Checkpoint/Rollback    Storage solutions    State machine repl.
   \                       |              |                      /
    \-------------------------------|---------------------------/
                          Protocol building blocks
                          OS infrastructure

---

Fault tolerance via replication has a rich history. At some point, people were
wondering whether it fits in the OS community. Now people wonder whether clouds
can support strong consistency and reliability.


## Fault tolerance in terms of safety

A fault-tolerant system maintains/restores an invariant even if something fails.
Usually, it also comes with a timeliness requirement.

FLP impossibility theorem (Fischer, Lynch, Patterson, 1985): In an asynchronous
network where messages can be arbitrarily delayed, there is no distributed
algorithm that solves the consensus problem.

CAP theorem: cannot have a highly available database under network partitions,
without introducing inconsistencies.

Byzantine faults: need 3f+1 replicas to tolerate f byzantine failures.

## System principles

Make core elements as simple as possible (Butler Lampson's hints, 1983). Pare
down, optimize the critical paths

Generalized end-to-end argument (Saltzer, Reed, Clark: End-to-end arguments in
system design)

How do systems *really* fail (Experience study by Jim Gray, 1985):
- Failures are caused by bugs, user mistakes, poor designs
- There weren't many malicious failures
=> Jim's advice: focus our efforts on where they are really needed.

## Tensions in fault tolerance

- Is fault tolerance and consistency too complex or costly?
- Do the existing mechanisms enable or impose solutions?

It all comes down to performance and scalability. The "real" metric that decides
whether a fault tolerance mechanism is published in SOSP is speed.

## OS primitives for fault tolerance

- IP multicast
  Just doesn't work...
- TCP
  No support for reliable transfer to multiple receivers
  Uncoordinated model for breaking connections on failure
  Byte stream model -- bad match for RDMA
- Higher-level primitives:
  Isis in 1985: State machine replication on objects
  Paxos: State machine replication. Provides a durable database on events.

Isis was perceived as too slow, there were delays on the critical path.

Isis was refactored in 1987 => Oracle
Introduces "virtual synchrony". Gives users choices, and they only pay for the
properties they use.

### How to speed systems up?

Start with a simple, easily analyzed solution. Study the code. Inefficiencies on
the critical path? Opportunities for pipelining and asynchrony?

Many examples: Isis, Paxos, ZooKeeper, Chain replication, Zyzzyva, ...

=> Real systems, but still informed by a sound theory.

### CATOCS controversy

Dave Cheriton and Dale Skeen argued that consistent replication is:
- too complex (to belong in the core system)
- violating the end-to-end principle (because it imposes models on the user)
- no matter what form of update order is supported, users won't like it
- too slow

In fact, the first versions of all these systems were way too slow. But later
versions were faster.

Still, an unanswered question was: what is the missing building block for
consistency?

### Winners and losers

Winners:
- State machine replication, Paxos, ACID transactions
- Real systems like Chubby, Zookeeper, Corfu
- Primary and warm backup, chain replication
=> These systems solve the problem for 3-5 nodes

For cloud, people rebelled against this idea. Cf. Dynamo: eventual consistency.
CAP theorem: can have two of {consistency, availability, and partition
tolerance}.

This lead to new ideas such as BASE (eBay, Amazon): Basically available, soft
state, eventual consistency. Start with a transactional design, but then weaken
the atomicity.

In today's cloud, there is a redefinition of consistency. Today, stale cache
reads have negative utility, but they don't cause safety violations.

### Homework (due: SOSP 2017)

- Start with a clean slate (but learn from the past)
- Embrace a modern architecture
- Propose a new approach to cloud-scale consistency

Maybe we'll get an actual fundamental building block for fault tolerance.
