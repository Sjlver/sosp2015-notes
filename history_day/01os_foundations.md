Perspectives on OS Foundations
==============================

Speaker: Peter Denning
Sunday, 09:00


History of OS development
-------------------------

batch -> interactive -> distributed -> cloud/mobile
1950              1970            2000         2010

Internet arrived in the 1970s


Development of Principles
-------------------------

Laws (statements that are always true). For example:

- c(t) = max( a(t), s(t) + 1 )
  Semaphore law
- M = (spacetime) * (throughput)
  Memory management
- Mean Value Equations
  Algorithm to evaluate queueing systems

Statements of design wisdom:

- Information hiding
- Levels of abstraction

For something to be a law, it should be *cosmic*: Cosmic = timeless + spaceless
(Jim Gray). Laws should be true at all times and in all places. Locality is a
cosmic principle. Virtual memory is a great idea, but is not a principle.

Some OS principles got into computer science:

- Interactivity
- Naming / mapping
- Concurrent processes
- Protection / sharing
- Locality
- Virtualization
- System languages

=> cf. Survey of principles at <http://greatprinciples.org>

In 1971, Operating Systems became the first non-math core course at MIT.


Example: Emergence of the locality principle
--------------------------------------------

First motivations in 1965: Performance of virtual memory, multiprogramming, and
thrashing. The question was, could virtual memory effectively be done?

Thrashing was a big problem. Nobody understood why it happened, or when it would
happen.

Then people made "reference maps" (Plots showing what area of virtual memory is
accessed at what time). This lead to the concept of locality sets. These are
stable over some time, and then transition to other locality sets.

Research results:

- The working set equals the locality set most of the time
- The working set policy (i.e., don't swap out pages from the working set) is
  nearly optimal in the number of page faults, and prevents thrashing.

Locality principle:

- Empirically, all computations display locality.
- There are theoretical reasons in algorithm design for this.
- It makes sense to harness locality (caches, ...)

Patterns learned from OS research:

- There is never certainty.
- Occasionally, an insight charts a new direction.
- Researchers are always searching what works.
- Theory always follows practice. People observe things, and then try to
  understand it better.


Challenges ahead
----------------

- End of "knowledge era". Computers now do tasks that we thought required
  knowledge.
- Networks have become a space of social power, actions and identities.
- Security and privacy in the Internet of things
- Size and complexity of systems

