Consistency at Facebook
=======================

Consistency eliminates anomalies and makes systems easier to program. However,
relaxed consistency models promise higher throughput and lower latency.

Throughput and latency are simple to quantify... Consistency isn't.

TAO is Facebook's eventually consistent cache. It replicates things in the
background. Two types of anomalies can happen:

- causal: B does not see a post that A sees
- "Oculus example": Both A and B think they are the first to post a comment.

This work collected traces at web servers to see how often users encounter such
anomalies.


Tracing
-------

Cannot log all requests => sampling. Sample *all* requests for *a small subset*
of objects. This is sufficient for checking local consistency. This work sampled
one object in a million.

Local consistency models include linearizability, per-object sequential
consistency, and read-after-write.

Challenge: time skew between servers. In one week, the 99.9th percentile time
skew was 35ms. This work thus adds 35ms as a slack value to all request
durations.

Sample size:
- 12 days of tracing
- 7 mio objects
- 3 billion requests


Checking Linearizability
------------------------

Linearizability = strongest non-transactional form of concurrency:

- Real-time constraint. Everything seems to happen at exactly one point in time.
- Total order constraint.

The checker constructs a graph where vertices are read/write operations and
edges are ordering relations. Read operations are merged with their
corresponding write operations. If cycles are detected during this merge, then
there is an inconsistency.

Demo: <http://tinyurl.com/sosp15-demo>


Results
-------

### Linearizability

Found 5 linearizability violations per 1 million reads.

4 of these violated the real-time constraint.

1 of these violated the total-order constraint.

### Per-object sequential consistency

Per-object sequential consistency has a total order constraint and a user
session constraint (users should see their own writes).


Real-time consistency monitoring
--------------------------------

The consistency checkers cannot run in real-time (otherwise, we could just make
the system consistent). The paper also contains the definition of
phi-consistency, which is suitable for real-time monitoring.
