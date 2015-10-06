Read-Log Update
===============

Goal: better programming framework for concurrency.

Performance depends on:

- unsynchronized traversals: when traversing data, read a lot of stuff at once
  without locking.
- multi-location atomic updates: use locks in specific places to hide data races
  from developers (?)

Solution: Read-copy update (RCU)

- Very popular, frequently used in Linux
- But complex to program for

Read-Log Update (RLU) is an extension to RCU that makes it simpler (?)

RCU overview
------------

- To modify an object, copy it and modify copies
- To commit, update a single pointer atomically. Garbage-collect old copies once
  all current read operations are done.

A problem is that RCU allows only for updating a single pointer. It depends on
being able to atomically switch to the new state.

RCU is also fairly hard to do... building a concrete implementation is often
worth a paper :)


RLU
---

RLU keeps both old and new pointers around. There is a clock that is incremented
on updates (atomically). Threads compare the clock value to their own clock, in
order to decide whether they should use the new or the old pointer.

RLU builds on existing RCU apis:

- `rlu_dereference`, `rlu_assign_ptr`, ...
- adds a new call: `rlu_try_lock`

The main simplification from using RLU comes from reads seeing a consistent
state.


Evaluation
----------

Show that RLU works great for something like a linked list. It also works for
things like hash tables, although the performance is a bit worse than RCU.

Experimented with Kyoto CacheDB, and showed that RLU made it possible to avoid
locking => great increase in performance.

<https://github.com/rlu-sync>
