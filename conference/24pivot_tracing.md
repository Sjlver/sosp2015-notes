Pivot tracing
=============

Q: how is disk bandwidth being used in the cluster?

Goal is to obtain a breakdown by top-level application.

Today, logging and monitoring code is added to a program during development. =>
Both not enough and irrelevant information. Modifying what's logged requires
patching the program or recompiling it.

Today, it is also hard to correlate events across multiple systems. This is
particularly true of systems that are dynamically composed of multiple services.


Pivot tracing:

- models events as tuples
- dynamically evaluates relational queries over this dataset
- introduces the happened-before join

Pivot tracing can collect tuples at "trace points" (like in aspect-oriented
programming).

The happened-before join is used to relate tuples across multiple parts of the
system.


Design
------

- Dynamic instrumentation
  Performed by a pivot-tracing agent

- Baggage abstraction allows for causal tracing.
  Baggage is a key-value container that's propagated along a request. It
  propagates state between trace points. 

Tracepoints export identifiers that queries can access: local variables, global
variables, special values like the host name.

Queries over these values are written in a language like LINQ. Such a query
produces a stream of tuples as output.

The query is compiled into "advice" which is installed at trace points. It can:

- observe values
- pack and unpack them in the baggage
- filter data
- export it as result


Prototype
---------

PT agent uses Javassist for dynamic instrumentation, and a Pub-sub system to
receive commands and send tuples.

Baggage uses Protobufs.

Several systems from Hadoop were modified to add baggage.

Pivot Tracing has a baseline overhead of 0.3%. In the worst case, overhead was
14.3%. The largest observed baggage size was 137 bytes.


Experiments
-----------

- Monitor queries
- Decompose request latencies
- Debug recurring problems

The talk showed how the authors debugged an HDFS performance anomaly, by finding
that some replicas were chosen much more frequently than others. They ruled out
some hypotheses for this prior to finding the right queries.
