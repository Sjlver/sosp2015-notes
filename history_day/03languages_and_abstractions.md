Perspectives on System Languages and Abstractions
=================================================

Speaker: Barbara Liskov
Sunday, 10:00

Talk about the early days of computing (< 1980s)
Focus on abstractions for structuring systems in the early days.


Single machine systems
----------------------

In the beginning, there was batch processing (put your program on a card deck,
submit it with tests to the computing center, get the data 24h later). Machines
were very expensive. The programs didn't use them efficiently, because only one
program was running (and had to wait for I/O, ...)

Then there was time sharing (many teletypes, everybody thought they had the
entire machine).

### The "THE" multiprogramming system

E. W. Dijkstra, 1967

A strictly layered system with independent users.

Layer 0. Processes and semaphores. Used for critical sections and IPC.
Dijkstra proved that there were no "deadly embraces" (what we today call
deadlocks).

### The design of the Venus operating system

Liskov, SOSP 1971

Implemented an instruction set in microcode, and used this to build a
time-sharing system.

Resources were presented through "layers of abstraction". A layer had some
hidden state and resources, and let higher-level code access these resources
through predefined operations.

=> what we would today call OOP.

There were two system models for accessing resources:

- Resource process model: each resource is controlled by a process, need IPC to
  access.
- User process model: processes use abstract data types to access resources
  safely. The code to access resources runs in the user process (might do IPC
  under the cover).

These models are duals (Lauer and Needham, 1978). For example, a port in the
resource process model corresponds to an operation in the user process model.

### Programming issues: multiplexing

How to multiplex operations and synchronize them?

Monitors (C.A.R. Hoare, 1974)

A Monitor is an abstract data type with an associated lock. The lock is acquired
automatically when operations are called.

Condition variables allow to release other processes when one process was
blocked.

Monitors in Mesa (Lampson and Redell, 1979): Identified the problem of nested
monitors, and "external" operations.

### Programming languages

Many programming languages were actually developed in the OS community:
- Modula and its later variants
- Concurrent Pascal
- Mesa

Interestingly, these papers have a syntax in BNF, but then explain the semantics
rather informally, with many examples. In contrast, the programming language
community cares a lot about semantics and typing equations, but doesn't really
give an English-language description of what a programming language does :)


Distributed Systems
-------------------

Motivation: Cheap workstations came about, connected by a LAN.

How to structure this? Clients and servers? Distributed heap memory?

For distributed systems, communication is required. But it's hard.

Implementing Remote Procedure Calls (Birrell and Nelson, 1984): Construction of
communicating programs was a difficult task, undertaken only by members of a
select group of communication experts.

Why?
- Need to link communication requests with replies
- Formatting of messages can be tricky
  E.g., different computers might not represent data structures the same way.

Using RPCs, local and remote calls look the same.

RPCs are cool and have been extended in various ways (promises, caching,
distributed lookup, ...) Have some problems with dropped packages (exactly once
if reply semantics, exactly once semantics, ...)
