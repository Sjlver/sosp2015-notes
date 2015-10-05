Distributed Transactions with consistency, availability, performance
====================================================================

Aleksandar Dragojevic


Build distributed transactions with no compromises, using new hardware trends:

- lots of DRAM
- non-volatile memory
- fast networks with RDMA

These trends remove many storage and networking bottlenecks. Only need to
optimize for CPU usage.

Three design principles:

1. One-sided RDMA operations
   High throughput, low latency, very CPU efficient
2. Reduce message counts in the protocol
   Example: four phases instead of two, but uses fewer messages.
3. Effectively use parallelism
   Example: new transactions can be performed during recovery.


Background
----------

FaRM distributed system platform from NSDI 2014. General platform for things
like key-value stores, OLTP databases, ... Memory of the whole cluster is
represented as a shared memory space.

Each machine in the FaRM cluster contributes most of its memory to the shared
address space (all data is in RAM). Objects are read from remote machines with
RDMA. Reads are consistent (see NSDI paper).

Network messages are written to circular buffers (one buffer per peer) directly
by the NIC. CPU polls these buffers to process messages.


Transactions
------------

Many optimizations for high performance:

- primary-backup replication (more CPU efficient to Quora, and better suited to
  data centers)
- optimistic concurrency control
- ...

Transactions are started by a coordinator. It can read as many objects as
required from the shared address space, and buffers writes. On commit, these
writes are applied atomically.

FaRM commit protocol:

1. Coordinator acquires lock for all the objects in the write set
2. Validate the read set. This ensures that objects have not changed since they
   were read in the transaction.
3. Replicate the log: The coordinator performs a one-sided RDMA write to all the
   objects that are written to.
4. Update and unlock

Even though there are four phases, it uses fewer messages than a two-phase
commit protocol. Also, all phases except locking are one-sided (require no CPU
processing at remote side, only hardware-generated ACKs), thus they have low
latency.

Complications: One-sided operations complicate recovery, because the remote side
cannot process or reject them.


High availability
-----------------

Some techniques to achieve high availability:

- Primary and backup are both stored in main memory
- Short failure detection time (10ms leases). There are dedicated network queues
  and high-priority threads to detect failures. Memory for failure detection is
  pre-allocated.
- Use parallelism
  New transactions can be done in parallel with recovery
  Transactions and data are recovered in parallel.

Failure recovery steps:

1. Detect a failure

   One of the FaRM machines is special. It's the "configuration manager" (CM)
   who's job is to detect failures of other machines (using heartbeat messages).

2. Change configuration

   CM uses ZooKeeper to perform a configuration change.
   CM remaps regions that are affected by the failure. This is done in a way
   that maximizes availability (try to spread replicas so that they can be
   quickly recreated from backups).
   CM then sends out the new configuration, and waits for everybody to ACK it,
   and then sends a final ACK that starts recovery.

3. Recover transactions

   First, the log is drained. The set of messages from the previous
   configuration is freezed.

   The backups then look for transactions that need recovering, and send them to
   the primary.

   The primary then locks all the affected regions. Once this is done, regular
   transactions can continue while the replication is running.

4. Data recovery

   Data is replicated in parallel (similar to RAMCloud)


Evaluation
----------

90-machine cluster. Each machine has 2x Infiniband (56Gbps)

Can get up to 140M transactions committed per second, at 60us latency.

During a failure, the throughput goes down to almost zero, but recovers within
50ms.

Data recovery takes a bit longer (20 seconds or so) but does not have a visible
effect on throughput.

Other experiments: failed 18 machines at the same time. Took 400ms to recover.
