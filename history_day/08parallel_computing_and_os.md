Parallelism and operating systems
=================================

Speaker: Frans Kaashoek (MIT CSAIL)

Parallelism is a great topic, because it's a major theme. It's either easy (when
there is no sharing) or super hard.

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

Many users and one computer. In the early days, batch processing was a pain for
debugging. IBM sliced time at 8-hour shifts.

Time-sharing exploits user parallelism: while one user is thinking, somebody
else uses the CPU. => cf. ["ctss wgbh" (1963) on Youtube][timesharing]

[timesharing]: https://www.youtube.com/watch?v=Q07PhW5sCEk


### I/O parallelism

Many users running many programs => opportunity for I/O parallelism emerges.

OS designer has to do the sweating, but users just write straight-line code.

In the old times, slices were 16ms (based on 1% switching overhead) and the
supervisor fit in 5000 words :)

Challenge: atomicity and coordination. The THE system introduced semaphores.
Interestingly, the semaphores were introduced in the appendix of the paper, at
the request of the program committee.

Semaphore operations were called P & V: stood for Dutch "passeren" and
"verhogen". But Dijkstra later changed his mind, and said it stood for
"verhogen" and "probeer te verlagen" (try to reduce). The terms are so
complicated because in Dutch, increase and decrease (verhogen and verlagen) both
start with a v, which would be confusing. (Frans Kaashoek, being Dutch himself,
explained this much more humorously than my notes can capture it.)


### Time-sharing and multiprocessor parallelism

Many ideas about parallelism in the 60s:

- Amdahl's law (1967)
- Deadlock detection
- Traffic control in Multics (Saltzer's PhD thesis, 1966)

Some things changed with minicomputers (PCs) because they had just one
processor. For example, Unix was designed for uniprocessors (locks implemented
by disabling interrupts, for example).


## 2. Client/server

Idea: allow non-kernel programmers to implement services.

These services must exploit I/O concurrency, because they serve many requests.
=> Kernels exposed primitives like threads, locks, ...

This lead to many high-impact ideas:

- New OSs (Accent/Mach, Topaz/Taos, ...)
- pthreads
- New programming languages (Mesa, Modula2, ...) These influenced Java, Go, ...

Threads were difficult to use. See for example "Programming with threads"
(Birrel, 1989. A tutorial. Still worth a read today.)

The tutorial warns programmers to use the following pattern with condition
variables:

    WHILE NOT expression DO Thread.Wait(m,c) END;

The reason to use WHILE instead of IF is that another thread might snatch the
lock away from us just after `Wait` returns.

Because not everybody read the tutorial :) and because threads were still hard
to use, there came a debate of events vs threads. John Ousterhout published "why
threads are a bad idea" in 1995. Lead to things like NodeJS.


## 3. Shared-memory multiprocessors

Around the mid 90s, cheap x86 machines with multiple cores came about. Suddenly,
Kernel- and server-developers had to take multiprocessing seriously.

Lead to much research:

- scalable locks
- NUMA
- efficient user-level threading
- RCU: read-copy-update
- scalable VMMs


## 4. Multicore processors

Because uniprocessor performance kept doubling every three years, there was no
real need for parallelism. But...

... Moore's law stopped.

Suddenly in the 2000s, processors started to increase the number of cores, no
longer the CPU frequency.

Linux started to support many of the NUMA features, and many applications scaled
well. There were also cases of thrashing (Frans gave an example: Exim. Scales to
24 cores, then performance goes down drastically. The reason was contention on a
cache line written by another core (reading such a cache line takes 100 to 10000
cycles).

It is hard to avoid cache line sharing. For example, even acquiring a read lock
requires a write. Solution: read-copy-update (RCU). Readers don't acquire any
lock. Writers make changes available using atomic instructions. Old nodes are
freed when all readers have left the critical section.

What does this mean for us?

- Commodity hardware will look differently.
  - 1000s of unreliable core?
  - heterogeneous cores?

- How to avoid struggling for programmers?
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
