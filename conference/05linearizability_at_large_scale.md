Implementing Linearizability at Large Scale and Low Latency
===========================================================

Goal (like in the previous talk): get consistency without losing all the
performance.

Approach: a separate layer for linearizability. RIFL is a "reusable
infrastructure for linearizability". It adds about 500ns of latency to
transactions (<5%).

Def: linearizability: An operation is linearizable if it appears to happen
exactly once and instantaneously. It's the strongest form of consistency for
concurrent systems. Most systems don't provide it.

Alternative provided by existing systems: at-least-once with idempotent
operations. This comes with some problems...


RIFL key ideas
--------------

1. RPC identification

   Each RPC has a unique ID. Retries use the same ID and can thus be detected.
   IDs contain a unique part, and a monotonically increasing per-client sequence
   number.

2. Durable completion record

   Store the RPC id and the result when the RPC is completed.

3. Retry Rendezvous

   Solves the problem of finding completion records even when data moves (e.g.,
   due to recovery).

   To migrate the completion record correctly, each RPC is assigned to one
   primary object. Its completion record moves along if the object is migrated.

4. Garbage collection

   Completion record should be garbage collected once we're sure that a client
   will never retry the RPC.

   RIFL uses ACKs piggy-backed on other messages. These are promises by a client
   that it will not retry an RPC. Also, completion records are deleted when a
   client crash is detected.


Evaluation
----------

RIFL runs on RAMCloud, a general-purpose in-memory key-value store. RAMCloud had
low latency (4.7us for a read, 13.5us for a write). RIFL adds 5% to that
latency, and no measurable effect on throughput.

RIFL is 1200 lines of C++.

RIFL can handle 1mio clients, because per-client state is only 116 bytes.


Case study: distributed transactions (Synfonia on top of RIFL)
--------------------------------------------------------------

Synfonia uses a two-phase commit protocol.

RIFL simplifies transaction recovery. The two-phase-commit `prepare` RPC was
made linearizable.

Evaluated using TPC-C, and compared against H-Store. Essentially, it beats
H-Store by one to three orders of magnitude in latency, and a factor of two in
throughput.


Linearizability as foundation?
------------------------------

Traditional DBMS have a lot of code to handle transactions, and then build
linearizability using transactions.

RIFL has quite a bit of code for linearizability, and implements transactions
using this. Seems more useful...?
