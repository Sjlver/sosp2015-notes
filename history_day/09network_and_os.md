The Network and the OS
======================

Speaker: David Clark (MIT CSAIL)

## From the specific to the cosmic:

Early issues were pragmatic and "mechanical".
- How to structure the code that implemented the protocols
- Performance

Later issues were more fundamental.
- Security


## What is specific about networking?

Networking is different from other I/O:

- variable size units
- everything is potentially malformed
- the Internet connected heterogeneous machines (on Multics, the machine had
  9-bit bytes, but the IP protocol had 8-bit fields...)
- unpredictable arrival and transmission types
- packets must be processed to demultiplex them, and demultiplexed before
  further processing... it's a mess :)

=> because of all these reasons, it was hard to rip networking code out of the
   OS.

In 1986, there was a deep confusion as to how to move from a protocol spec to
its implementation. Why was it so hard? Was the difficulty in:

- Implementing the state machine?
- Marshalling the package fields?
- Dealing with errors?
  They were so inexplicable...
- Processing 32-bit numbers on a 36-bit system?
  TCP sequence numbers could wrap around... this had to be simulated on 16-bit
  machines :)
- Copying the data?
- Dealing with congestion control?
- Dispatching the packet to the right connection?
- Dealing with layers?

People didn't know where to put the protocol. Should it go in the user process
(but then there's no asynchrony...)? In the OS (but that's much harder to
program)? In a separate process (but that has bad performance; it takes much
longer to schedule a process than to process a packet...)?

Protocols had an odd structure: they needed to wait for multiple events (packets
arriving, user sending more data, timers firing). This didn't really match some
of the early scheduling primitives.

Performance: people didn't know the relative cost of events:
- process a header
- schedule a process or thread
- setting a timer
- taking an interrupt
- copying data around
- dispatching a packet

People initially thought that the protocol processing was the expensive step.
Turns out, processing the header was the cheapest of all these steps...

### Example: TRIPOS

TRIPOS was a microkernel system where networking code was very elegant, split
over three processes.

However, 54 process wakeups were needed to exchange a single character (for
remote login). Re-writing it as a single process made it 10x smaller, 10x
faster.

Some papers came out of this:

- The structuring of systems using upcalls (1985)
- Hashed and Hierarchical Timing Wheels: Data Structures for the Efficient
  Implementation of a Timer Facility (1987)
  Timers that took constant time to set, instead of O(number of timers).
- An analysis of TCP processing overhead (1989)
  Counted assembly instructions for various operations. For example, receiving
  an ACK turned out to be more expensive than receiving the data...

Some insights, too:

- "Damn, speed of light is not on Moore's law curve."

### The recurring structural issue

Networks have a distinct set of issues to solve. But, networks don't know what
they are being used for (end-to-end model).

Thus, TCP persists because we couldn't find anything else that is general enough
to be part of the OS. Still, the question remains whether a protocol should be
in the core or not...

The network cannot trust the host, the app, ...
