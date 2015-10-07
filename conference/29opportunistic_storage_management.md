Opportunistic Storage Maintenance
=================================

What is storage maintenance?

- backups, virus scans, defragmentation, garbage collection...
- access large amounts of data

It's hard to decide when to do maintenance, because these tasks can interfere
with other workloads. Today, we schedule such tasks at night or while devices
are idle.  => Today's tasks assume there will be idle time or downtime.

Observations:

- Tasks often access the same data.
  Yet, caches don't help because tasks are independent.

- Tasks might access files in a different order or at different times.
  Thus, performance drops with the number of tasks.

The main insight is that tasks are often order-independent (e.g., it doesn't
matter if file A is backed up before file B).

OSs have a cache of recently accessed files that still reside in memory. Tasks
can look at this cache to see whether there are opportunities. This works
exposes an API to do so:

- tasks register events (add, remove, ...)
- events can be filtered by path, file type, ...
- task polls for such events


Duet design
-----------

Duet is a kernel module that listens to page status changes. It exposes such
events to userspace tasks.

A page does not necessarily correspond to an entire file. Duet tells a task how
many pages of a file are cached => task can prioritize the file that has the
most cached pages.

The tasks don't need to be modified heavily. They usually already have a loop
that goes over all files. With Duet, this loop would use a priority queue as
data structure, and call into Duet to modify file order.


Applications
------------

- Btrfs scrubber, backup and defragmentation
- Garbage collection in F2fs
- Rsync <- prioritize files with the most cached pages

<https://github.com/gamvrosi/duet>
