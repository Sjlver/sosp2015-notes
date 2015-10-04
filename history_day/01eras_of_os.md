Eras of Operating Systems
=========================

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

- c(t) = max( a(t), s(t) + 1)
  Semaphore law
- M = (spacetime) * (throughput)
  Memory management
- Mean Value Equations
  Algorithm to evaluate queueing systems

Statements of design wisdom:

- Information hiding
- Levels of abstraction

Cosmic = timeless + spaceless (Jim Gray). A guideline for deciding whether
something is a law. Laws should be "cosmic", true at all times and in all
places. Locality is a cosmic principle. Virtual memory is a great idea, but is
not a principle.

OS principles got into computer science:

- Interactivity
- Naming / mapping
- Concurrent processes
- Protection / Sharing
- Locality
- Virtualization
- System languages

=> Survey of principles at <http://greatprinciples.org>

In 1971, Operating Systems became the first non-math core course at MIT.


Example: Emergence of the locality principle
--------------------------------------------

First motivations in 1965: Performance of virtual memory, multiprogramming, and
thrashing. The question was, could virtual memory effectively be done?

Thrashing was a big problem. Nobody understood why it happened, or when it would
happen.

Then people made "reference maps" (what area of virtual memory is accessed at
what time?). This lead to the concept of working set. These are stable over some
time, and then transition to other locality sets.

Research results:

- The working set equals the locality set most of the time
- The working set policy (i.e., don't swap out the working set) is nearly
  optimal in the number of page faults, and prevents thrashing.

Locality principle:

- empirically, all computations display locality
- there are theoretical reasons in algorithm design for this
- it makes sense to harness locality (caches, ...)

Patterns learned from OS research:

- There is never certainty.
- Occasionally, an insight charts a new direction
- Researchers are always searching what works
- Theory always follows practice. People observe things, and then try to
  understand it better.


Challenges ahead
----------------

- End of "knowledge era". Computers now do tasks that we thought required
  knowledge.
- Networks have become a space of social power, actions and identities
- Security and privacy in the Internet of things
- Size, complexity and size of systems.
