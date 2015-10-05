Using Crash Hoare Logic for Certifying the FSCQ File System
===========================================================

People keep finding bugs in file systems.

Existing work doesn't handle crashes particularly well. Thus, the goal of FSCQ
is to certify an entire file system that actually runs. The proof includes cases
when the machine crashes, and even when it crashes during recovery, etc.

Contributions:

- Crash Hoare Logic
  Automates part of the proof effort
  Proofs mechanically checked by Coq
- FSCQ

Both CHL and FSCQ were run through Coq, which checked that it was OK.

Coq source code was estracted to a haskell implementation, that was compiled to
a FUSE module.

Interesting demo: Ran this using a USB thumbdrive, took the thumbdrive out while
a mailserver was using it, then mounted it again.


Specification
-------------

POSIX is not very explicit about crash safety... Thus, FSCQ has its own spec.

Essentially, every file-system call is executed inside a transaction.

FSCQ uses a fairly-standard write-ahead log on disk.

The difficulty is to formally specify what it means to crash, and to specify
what sort of state the system might be in during recovery.


CHL
---

CHL extends Hoare logic with the notion of crashes. It says that, in the event
of a crash, the system is either in pre or in post state (but nothing else).
This matches what file systems assume about disks (i.e., blocks are either
written or not).

CHL certifies large procedures by automatically linking post-conditions of a
basic block with pre-conditions of its successors.

Most of the remaining proof effort is about proofing representation invariants.

System calls have a spec that looks fairly straightforward, except for the crash
part. This part enumerates all sort of things:

- crashes before the procedure starts
- crashes after
- crashes at various stages, with the corresponding outstanding transactions...


Proofing recovery
-----------------

Spec for recovery simply says that it needs to be idempotent.


FSQC
----

Simple file system, modeled after v6 Unix, with write-ahead log.

Some tricks to make proofs easier: For example, to find a zero-bit in a bitmap,
re-use the proof for a marshalling library and marshal the bitmap into a
sequence of 1-bit elements.

Some simplifications: hard-links are not supported, so that the file system is a
tree instead of a graph. This makes it much easier to reason about.


Results
-------

Eliminates many types of bugs: violating file invariants, corner cases, ...

However, there are also bugs that are not eliminated:

- FSCQ might return wrong error calls because the spec is not super precise
- FSCQ might run an out-of-space error even if there is space available, because
  the spec does not force it to be efficient.

FSQC is 30'000 lines of code (about 10x of an equivalent teaching file system).

Interestingly, changes to the system could often be done in a fairly localized
manner, without having to re-proof everything. This is due to two factors: (1)
proof automation, and (2) careful modularization.

Performance comparison with xv6 (similar system) and ext4 in performance mode.

FSCQ performs similarly compared to xv6, slowdown is mostly due to Haskell.
There is a ~100% slowdown compared to ext4.

<https://github.com/mit-pdos/fscq-impl>
