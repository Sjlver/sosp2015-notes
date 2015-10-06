Vuvuzela: a scalable private messaging system
=============================================

David Lazar.

Metadata is an important problem... it tells a lot about our communication.

Goals:

- work even if most servers are compromised
- scalability: support a lot of users

Vuvuzela provides strong privacy (with differential privacy) and scales to
millions of users.

Vuvuzela is a handful of servers arranged in a chain. It has two protocols: (1)
dialing and (2) conversation.

Threat model: Assume that all but one server are compromised. All users (besides
your friends) may be malicious.

We want an adversary to not know whom Alice is talking to (or whether she is
talking at all).

Idea:

- encrypt as much metadata as possible
- add noise to everything else
- use differential privacy to figure out how much noise to add.

Talking via dead drop:

- Alice and Bob decide on a random dead drop
- If a dead drop encounters two messages, it swaps them and sends them back

Dead drops are long pseudorandom strings, so they don't reveal metadata.

Users who aren't in a conversation also send messages to random dead drops.

Mixnet hides origin of messages. This is what the chain of servers is for. Each
server on the chain shuffles messages, so that the last server (that manages the
dead drops) does not know who sent any message.

Dead drops with two messages still reveal communication (and an adversary could
nuke suspected clients and see if duplicates go away). To make this hard,
servers add noise, in other words, messages that go to the same dead drop.


Differential privacy guarantees
-------------------------------

Differential privacy:

    P( observation | Alice talked ) ~ P( observation | Alice did not talk )

How to add noise?

- Want to bring the two distributions epsilon-close together
- Can't have negative dead drops
- => need a lot of noise
- Another problem is that privacy decreases every round.

Vuvuzela adds as much noise as possible, and then uses differential privacy to
compute how much privacy users actually get.


Implementation
--------------

3000 lines of Go: <https://github.com/davidlazar/vuvuzela>


Evaluation
----------

With 1mio users, Vuvuzela has around 36 seconds of latency.

Vuvuzela is CPU bound, dominated by mixnet operations

Vuvuzela has a high bandwidth cost (12kbps for clients)
