Memory of File Systems
======================

Speaker: Mahadev Satyanarayanan
Sunday, 11:00

Question: what are the four fundamental forces in OS?

1. The quest for scale (from early 50s)
   How to get enough memory for my stuff to run?
2. The quest for speed (from early 50s)
3. The quest for transparency (from early 60s)
   People wanted portability, standardized architectures, ...
4. The quest for robustness (from mid-to-late 60s)
   Robustness against both system errors and human errors.

## 1. The quest for scale

Slide: memory prices in $ per megabyte (Exponential decrease pretty much from
1960 to 2010. Thirteen orders of magnitude reduction.)

Naming and addressability challenges:

- People consistently used not enough bits to address memory (12-bit, 16-bit,
  ...)
  "Who could possibly need more than a megabyte of memory"

- Semantic addressing
  Hierarchical namespaces, SQL, search engines

- Content Addressable Storage (aka deduplication)
  Venti (late 1990s), LBFS (early 2000s)
  Use a cryptographic hash of an object as its address.

- Capability-based addressing
  Short-term capabilities (a form of caching of expensive access checks)
  Long-term capabilities (Hydra in 1970s, Intel iAPX in 1981). Not much talk
  about this anymore...

## 2. The quest for speed

Processor speed doubles every 18 months, memory speed every 10 years.
=> Gap grows by 50% every year.

Before 1980, memory bandwidth grew with CPU bandwidth.

The answer to this have been *memory hiearchies*: The scale of memory appears to
be that of slower but more scalable technology. The speed appears to be that of
faster but less scalable technology.

The *working set* idea characterizes the goodness of fit of the hierarchy.

Exploiting parallel data paths (e.g., striping, sharding, bittorrent, ...)

One interesting thing: LRU works very well, consistently over time.
Alas, a few workloads defeat it: sequential scan (zero temporal locality) and
purely random access (no point in being smart about caching things).
Best solution so far: ARC (adaptive replacement cache, Megiddo and Modha, 2003).

## 3. The quest for transparency

Transparency: implementation is *indistinguishable from the original
abstraction*.

Important because
- "old code works"
- no unpleasant surprises for users

Hugely important in industry, less so in academia.

Achieved by interposing new functionality at widely-used interfaces:
- memory abstraction (hardware caches)
- POSIX distributed file systems
- x86 virtual machines

### Landmarks:

Distributed caching

Question: how to cope with consistency? Eventual vs strict models.
Do we hide from applications the fact that costs are not uniform?
Caching first presented in 1961 (Dynamic storage allocation in the atlas
computer).

Hardware caches, Distributed file systems, Web caching, VM state caching,
Key-value stores

## 3. The quest for robustness

How to cope with failures?

- Wear levelling
- ECC
- ...

How to cope with human errors?

- Apple time machine, elephant file system, even the "Trash" and "Undo"
  mechanisms.

Is the file system dead?

It appears true on a higher level, e.g., Android users never see files anymore.

However, file systems are hierarchical not because we don't know any better, but
because they match the limits of our cognition. => "The architecture of
complexity".
