Building Consistent Transactions with Inconsistent Replication
--------------------------------------------------------------

Transactions are sort of expensive, because they require

- a transaction protocol (two-phase commit or so)
- a replication protocol (Paxos or so)

Existing systems today *waste work* by having consistency at both layers. This
work argues that we can get the same strong guarantees even while relaxing the
consistency of the replication protocol.


Inconsistent Replication
------------------------

IR is a new replication protocol that provides *unordered operations* while all
replicas *agree on the set of operations*.

IR guarantees:

- fault tolerance (f failures with 2f+1 replicas)
- agreement about the set of operations of a majority of replicas
- overlap with previously added operations on at least one replica

IR protocol:

1. Execute operation at replicas.
2. If a quorum succeeds, the protocol considers the result to be successful.
3. If not, the application protocol picks one of the possible results.
4. It then updates this result at all replicas.

Benefits:

- Low latency: 1 RTT default, 2 in the worst case
- No cross-replica coordination or leader election needed
- Less general than state-machine replication or similar systems
  => needs careful co-design of application protocol


TAPIR: Transaction application protocol for IR
----------------------------------------------

Uses two-phase commit across partitions, and relies on IR for fault-tolerance
inside partitions.

TAPIR uses optimistic concurrency control to detect conflicts on IR.  OCC checks
one transaction at a time. IR ensures that every pair of transactions is checked
on at least one replica.

TAPIR uses client clocks to attach a timestamp to transactions. These makes it
*likely* (not certain) that replicas will commit transactions in the same order.
This gives a higher performance but is not needed for safety.

IR periodically synchronizes inconsistent replicas.


IR/TAPIR co-design
------------------

Fast, because transactions can be committed in a single round-trip.

At the same time, strong because transactions are still linearizable.

Complexity is hidden to applications.


