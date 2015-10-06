Paxos Made Transparent
======================

Paxos is a general building block to achieve availability. It ensures that a set
of replicas receives the same client requests in the same order. The replicas
are modeled as deterministic state machines, and thus they stay in sync.

Challenges of applying Paxos:

- Need an SMR consensus input interface.
  Hard (see papers: Paxos made simple, Paxos made practical, ...)
  Use an existing system? Zookeeper and Chubby are popular
- Need to model your program as a deterministic state machine.

This work takes *any* system (i.e., a black box), and wraps it into a
transparent replication layer.

Challenges:

- POSIX sockets: Crane ensures that all replicas see the same order of POSIX
  socket calls from clients. => Paxos consensus invoked on each socket call.
- Thread nondeterminism => leverage deterministic multithreading.
  Crane uses a logical clock value that serializes inter-thread communications.
  This is provided by an existing system, Parrot.
- Input timing: arrival times of requests might influence the result in the
  presence of concurrency.
  Crane uses "time bubbles", artificial delays that are introduced between
  bursts.
- Transparent checkpoint/recovery
  Uses CRIU (checkpoint/restore in userspace) and LXC (Linux containers)


Evaluation
----------

Used Apache, Mongoose, ClamAV, MediaTomb, MySQL.

All five systems ran without modification.

Performance was pretty good... response times were between 98% and 250%

Handling failures:

- Killing the primary replica invoked leader election. Took 2ms to elect a new
  leader.
- Killing a backup replica had negligible impact on performance.


Limitations
-----------

Crane assumes that the program uses (only) standard POSIX sockets as input, and
that it uses Pthreads synchronization.

Overhead from deterministic multithreading can be large.

<https://github.com/columbia/crane>
