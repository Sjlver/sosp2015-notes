Cross-checking Semantic Correctness
===================================

Idea: Find bugs by correlating many similar implementations of a program.

If one implementation deviates from most others, it is likely that the many are
correct and the deviant one has a bug.

This can find many types of bugs:

- condition check
- argument use
- differing return values, ...


Juxta architecture
------------------

Perform symbolic execution of file system functions (starting at VFS entry
points).

Then, compare the resulting symbolic environments.

### 1. Identify semantically similar entry points

Linux file systems have an abstract base class, VFS (Linux virtual file system).
There are interfaces like `inode_operations` that contain function pointers for
a specific implementation.

### 2. What to compare?

Juxta performs symbolic execution => rich resulting environment that describes
in detail what a function does.

The symbolic environment collects side effects, return values, kernel function
calls, ...

This results in a 300GB database of path constraints and associated effects.


Juxta then compares

- numeric ranges (e.g., flag values)
- occurrences of events (e.g., how often is a particular API flag accessed?)

For each data type, Juxta does statistical comparison

ranges => histogram-based comparison
occurrences => entropy-based comparison

### Histograms

Histograms contain average flag values over all explored implementations. Most
individual implementations will have histograms that are close to the average
histogram. Juxta then computes a distance metric between an implementation's
histogram and the average, and orders implementations by "deviantness".

Histogram-based comparison found 59 new bugs.

### Entropy

Juxta uses entropy to differentiate between "random" events (used by some
implementations, not others => hard to predict) and "expected" events (used by
most or very few implementations).

Entropy-based comparison found another 59 bugs.


Implementation
--------------

Built on the Clang static analyzer (6K lines of C++) plus 3K lines of tooling
written in Python.


Results
-------

Juxta checkers generated 2382 bug reports. 710 of these were verified manually,
and this lead to 118 bug reports.

One limitation is that deviations are not always bugs. 24 patches were rejected
by developers because they weren't real problems.

One interesting future work: Juxta identifies things that most or all file
systems do. Should such things be moved to the VFS base class?
