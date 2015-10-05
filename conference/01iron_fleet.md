IronFleet
=========

Goal: build complex distributed systems that are provably safe and live.

- Correct implementations
- Machine-checked proofs
- Both safety and liveness properties (e.g., no deadlocks)

Assumptions:

- Spec is correct
- Hardware is correct
- Compiler and OS are correct

Built:

- IronRSL: Replicated state library (runs Paxos)
  Interesting because of many "real-world" features, e.g., batching.
  Proved equivalent to running things on a single machine (but more reliable)
- IronKV: Sharded key-value store
  Proofed equivalent to a non-sharded, centralized version.

Builds on existing research:

- Advances in single-machine systems like Sel4
- Advances in SMT solvers
- IDEs for verification

Message and demo => You too could write code that works the first time you run
it.

IronRSL
-------

Key safety property: equivalent to a single machine
Liveness: client eventually receives a reply.

### Methodology: Two-level refinement

In refinement, the spec is an abstract state machine. We prove that each
transition in the real system corresponds to some state transitions in the spec.
The real system *refines* the spec.

Proving correctness is hard:

- Need to reason about distributiveness, global invariants, ...
- Concurrency

- ... but also implementation complexity, such as int overflows

Two-level refinement proves the two groups of properties separately, by
introducing a "protocol layer" between the spec and the actual implementation.
The protocol layer can use idealized structures such as mathematical numbers.

### Methodology: concurrency

Hosts are single-threaded, but stuff on multiple hosts runs concurrently. Thus,
we have to reason about all possible interleavings of things.

Concurrency containment:

- Event handler does all receiving before sending any message
- Event handlers are considered atomic

Argument: for every real trace, there exists a corresponding trace with atomic
host steps. Thus, it's without loss of generality that we can consider only
atomic steps.

Thanks to these restrictions, we can proof correctness by showing that
invariants hold in all states.

### Methodology: Liveness properties

Developer establishes a chain of conditions. The first condition is assumed to
happen (e.g., a message arrives), and the last condition is the desired outcome.

Developer then proofs that each step must lead to the next.

Simplified example: Client receives request > Replica receives request > Replica
suspects leader > Leader election starts.

How to proof a link?

- Show that the corresponding action is enabled
- Show that the action is effective

The first step is hard to automated proofers. IronFleet introduces
"always-enabled actions". These have guards so they sometimes do nothing, but
it is always possible to run them. Then, simply proof that actions are executed
infinitely often.


Verified libraries
------------------

IronFleet built some general-purpose verified libraries:

- Reasoning about temporal logic (e.g., induction, liveness chain proofs)
- Parsing and marshalling


Evaluation
----------

Few lines in spec (~1000)

Safety proof to code ratio is 5:1 (similar to IronClad, where it is 4:1)
Liveness proofs increase proof to code ratio to 8:1

Performance is within ~40% of a reference implementation. One nice thing is that
two-level refinement makes it easier to add optimizations, because the protocol
is not necessarily affected by it.

IronKV gets 25% to 75% of the performance of Redis.

<https://github.com/Microsoft/Ironclad>
<http://rise4fun.com/dafny>
