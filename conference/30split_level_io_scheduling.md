Split-level IO scheduling
=========================

Claims that a few years of scheduling improvements has been wasted :)

This work prevents a new framework:

- allow scheduling at multiple levels
- tag requests to identify their origin
- allow to implement new schedulers by implementing a few hook functions


IO scheduling frameworks
------------------------

The framework is an environment where schedulers run.

These schedulers are implemented by writing callback functions called by the
framework.

### Reordering

Schedulers are essentially reordering requests.

This is not always easy because of *entanglement*. For instance, two requests
might share the same metadata block => they are processed at the same time even
though they have different priorities.

*Write dependencies* are another phenomenon that prevents reordering. They cause
file systems to carefully order writes. This prevents the scheduler from
reordering them. In current frameworks, the scheduler cannot communicate with
the file system. It also cannot re-order accesses before they reach the FS.

Split-level IO scheduling introduces hooks above the file system to gate data.

*Write delegation* is another problem. For example, from the perspective of a
scheduler, it might seem that all requests come from the same point (cache
writeback daemon) whereas in fact they are caused by different apps. Split-level
IO adds tags to requests to give provenance information to the scheduler.
