Parallelism and operating systems
=================================

Speaker: Frans Kaashoek (MIT CSAIL)

Parallelism is a great topic, because it's a major theme. It's either easy (no
sharing) or super hard.

The main theme of research is focused on avoiding struggle for programmers.

1960s: IBM Stretch tech report. Noted a tendency toward increased parallelism.

Parallelism in operating systems:

- User parallelism
- I/O concurrency
- multiprocessors parallelism

Four phases:

1. Time sharing
2. Client/server
3. SMPs
4. Multicore (2005 to now; all software is parallel)


## 1. Time sharing

Many users and one computer. Batch processing was a pain for debugging. IBM
sliced time at 8-hour shifts.

Time-sharing exploits user parallelism: while one user is thinking, somebody
else uses the CPU. => youtube "ctss wgbh" (1963)


### I/O parallelism

Many users running many programs => opportunity for i/o parallelism emerges.

OS designer has to do the sweating, but users just write straight-line code.

In the old times, slices were 16ms (based on 1% switching overhead) and the
supervisor fit in 5000 words :)

Challenge: atomicity and coordination. The THE system introduced semaphores.
Interestingly, the semaphores were introduced in the appendix of the paper, at
the request of the program committee.

P & V: stood for "passing" and "release". But Dijkstra later changed his mind,
and it stood for "increase" and "try to reduce". (nice anecdote, my notes don't
quite capture it).


### Time-sharing and multiprocessor parallelism

Many ideas about parallelism in the 60s:

- Amdahl's law (1967)
- deadlock detection
- traffic control in multics (Saltzer's PhD thesis, 1966)

Some things changed with minicomputers (PCs) because they had just one
processor. For example, Unix was designed for uniprocessors (locks implemented
by disabling interrupts, for example).


## 2. Client/server

Idea: allow non-kernel programmers to implement services.

These services must exploit I/O concurrency, because they serve many requests.
=> Kernels exposed primitives like threads, locks, ...

This lead to many high-impact ideas:

- New OSs (Accent/Mach, Topaz/Taos, ...)
- Pthreads
- New programming languages (Mesa, Modula2, ...) These influenced Java, Go, ...

"Programming with threads" (Birrel, 1989. A tutorial. Still worth a read today.)

For example, warns programmers against (if cond; then ...; should be while
(cond), then do...)

Debate of events vs threads. People started thinking about "why threads are a
bad idea". Lead to things like NodeJS.


## 3. Shared-memory multiprocessors

Around the mid 90s, cheap x86 machines with multiple cores came about. Suddenly,
Kernel- and server-developers had to take multiprocessing seriously.

Lead to much research:

- scalable locks
- NUMA
- efficient user-level threading
- read-copy update
- scalable VMMs

Because uniprocessor performance kept doubling every three years, there was no
real need for parallelism. But...


## 4. Multicore processors

... Moore's law stopped.

Suddenly in the 2000s, processors started to increase in #cores, not in CPU
frequency.

Linux started to support many of the NUMA features, and many applications scaled
well. There were also cases of thrashing (example: Exim. Scales to 24 cores,
then performance goes down drastically. The reason is that it's very expensive
to read a cache line written by another core (100 to 10000 cycles).

It is hard to avoid cache line sharing. For example, even acquiring a read lock
requires a write. Solution: read-copy update (RCU). Readers don't acquire any
lock. Writers make changes available using atomic instructions. Old nodes are
freed when all readers have left the critical section.

What does this mean for us?

- commodity hardware will look differently
  - 1000s of unreliable core?
  - heterogeneous cores?

- how to avoid struggling for programmers
  - transactional memory?
  - develop frameworks for specific domains? (MapReduce, GraphX)


## Some observations

SOSP and OSDI papers had tremendous impact. Many ideas can be traced back to
them.

Things that worked well:

- Threads and processes
- Shared memory and locks
- Both threads and events have worked well... use them both?
- Surprisingly, it has been possible to adapt OSs to new hardware.
