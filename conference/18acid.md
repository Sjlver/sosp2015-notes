Callas: Modular Concurrency Control
===================================

Insights: decouple abstractions from implementation

=> Use specialized concurrency control for each group of transactions.

   This enables aggressive optimizations specialized for a given group.

Correctness criteria: A set of transactions is serialized correctly if there are
no cycles in the transaction dependency graph.

In Callas, this needs to hold both in-group, but also among transactions of
different groups. Nexus locks is the mechanism of choice for inter-group
consistency.


Nexus locks
-----------

Nexus locks is like a normal lock: stops two transactions from different groups
to access an object concurrently.

However, transactions from the same group are allowed to hold the same lock.
This is a bit too lax, thus...

... Nexus locks have an enforced release order. Transactions must hold locks
until their in-group dependent transactions are over.


In-group mechanisms
-------------------

Transaction chopping is an old technique to improve performance. It has
limitations as to when it can be applied. Grouping transactions causes these
limits to arise less often => can do more aggressive chopping.
