Arabesque: Graph Mining
=======================

Graphs are ubiquitous. Graph mining algorithms focus on finding particular
subgraphs. There are many applications:

- community detection
- characterize gene/protein interactions
- ...

Challenge: the number of subgraphs in a graph grows exponentially (with subgraph
size and with the overall number of nodes or edges).

Arabesque aims to be a new graph processing framework: "Think like an embedding"
(i.e., Pregel for graph analytics). It also has a nice logo :)

Arabesque has an API that is called with embeddings, and allows users to filter
and process them.


Design
------

Arabesque starts with small subgraphs, and iteratively expands them. A graph of
size n is expanded to a graph of size n+1 by joining one vertex.

In Arabesque, subgraphs (aka embeddings) are first-class citizens. Arabesque
finds them, handles automorphisms, does aggregation and load balancing. The user
decides how to *filter* and *process* the subgraphs.

Arabesque works in steps. A step takes as input a set of subgraphs. It then
expands these and generates *candidates*. These are passed to the filter
function to decide whether they should be kept. Graphs that pass the filter are
given to the process function, which can produce output. They are also passed to
the next step.

One problem: filtering might require access to aggregated values (e.g., when
filtering frequent subgraphs, we need to know their global frequency). For this,
Arabesque allows to run an aggregation function in parallel with the
exploration.


Architecture
------------

Arabesque runs on Hadoop. Each worker gets as input a partition of
size-n-embeddings, and generates as output size-n+1-embeddings. The output of
each step is shuffled for load balancing.


Optimizations
-------------

Avoid redundant work => find automorphisms.

The challenge is finding such automorphisms without requiring communication
between workers => *decentralized embedding canonicality*

Another optimization: handling exponential growth => *Overapproximating DAG* data
structure.

The idea is to store embeddings in a less restrictive superset. These supersets
contain spurious extra embeddings, which are later filtered out using
canonicality checks and user-defined filter functions.

Consider the matrix of all embeddings (where each row is a set of nodes).
Arabesque compresses pairs of adjacent nodes separately.


Conclusion
----------

I think this was kinda cool, because it gives developers a fairly simple API.

<http://arabesque.io>
