COZ: Causal Profiling
=====================

Goal: find code that matters for performance.

Profilers tell you which code runs frequently, and which code runs for a long
time. This is not always useful (example: the loading animation runs frequently
and takes a long time...)

A causal profile tells users *where optimizations makes a difference*.

A causal profile is a plot that shows how much the overall program becomes
faster if a given component is sped up.

A causal profiler runs performance experiments. If it could *magically* make a
stage faster, and measure the result...

COZ does without magic, but provides a "virtual speedup". While a component
runs, everything else is made a bit slower.


Progress points
---------------

Bob can mark something as a progress point... this indicates that the program
did something useful. Progress points measure throughput, so the causal profiler
can tell you how much some speedup affects throughput.

Progress points can be used in pairs (increment counter, decrement counter) in
order to measure latency. Use Little's law.


Example evaluation
------------------

Ferret image comparison library: COZ found the relative importance of stages.
Using this result, the developer changed the (static) thread allocation. This
lead to a 27% increase in throughput.

In the dedup compression library, COZ found that a loop that processes a linked
list in a hash table bucket was important. Developers then tried to increase the
size of the hast table to get rid of collisions; this didn't work. Then they
suspected the hash function, which was indeed bad => 9% speedup by slightly
changing the hash.

In SQLite, COZ identified an indirect call as important. Eliminating it lead to
a 25% speedup. Existing profilers found that only 0.15% of the runtime was spent
in the functions with this call.

<http://coz-profiler.org>
