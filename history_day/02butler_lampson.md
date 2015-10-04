Perspectives on Security
========================

Speaker: Butler Lampson
Sunday, 09:30


How did we get here?
--------------------

In the beginning, security was physical isolation: you bring your data, control
the machine, and take away the printouts.

We still do this today with VMs and Crypto, and maybe Enclaves.

Timesharing brought the basic dilemma: Isolation vs Sharing. Since then, things
have steadily gotten worse.

Wisdom:

"If you want security, you must be prepared for inconvenience" (some US general)

We know how to secure something simple, very well
Protect complecity by isolation an sanitization
We're also good at staging security theatre :-)

We're not good at making something complex secure
making things secure if they are not isolated, or change.

Themes:

- Goals: secrecy, integrity, availability
- Gold standard: authentication, authorization, auditing
- Principals: People, machines, programs
- Groups and roles: make policy manageable

Oppositions:

- Convenience (wins) vs Security (loses)
- Sharing vs Isolation
- Bug fixes vs Correctness
- Policy and mechanisms vs Assurance
- Access control vs Information flow

Timeline:

Timesharing -> Workstations -> PCs & Web -> Web & JavaScript -> Web & big data


Foundational concepts
---------------------

### Isolation: a hosts isolates an execution environment from the other

- Sandboxing
- Modules and objects in programming languages
- Software fault isolation
- Processes
- Virtual machines
- Airgaps

### Safe sharing: access control

- Isolation boundary limits access to use channels
- Access control for channel traffic
  Authenticate "principals": who makes the request
- Policy sets the rules
  Authorize access: group principals or resources to simplify management.
- Plus an audit log

How to evaluate the policy? It's a function of the subject and object. Except
instead of "function", it's called "access matrix".

Permissions kept at the object are ACLs. Permissions kept at the subject are
capabilities.

Capabilities are called file descriptors, handles, objects in programming
languages...

### Keeping secrets: information flow

- Labels on information
- Isolation boundary limits the flow to channels
- Flow control based on labels

Invented to model military classification (top secret > secret >
confidential...). Labels form a lattice.

Decentralized flow control: (Liskov and Myers in 1998): Anybody can invent
labels, and the owners of a label can declassify it. So far, not really
practical.

And then, there are covert channels...

### Who controls policies: DAC, MAC, RBAC

User controls policy: discretionary access control. But the user might be
malicious. Or incompetent... Or does not care about policy.

Mandatory access control: an administrator decides the policy.

Role-based access control: the app designer decides policies, and the admin just
populates the roles.

### Distributed Systems: Cryptography

Distributed systems need to communicate => Secure channels.

Encryption and signatures give an end-to-end secret channel. In 1977, public key
crypto came along.

In 2009, homomorphic crypto came along (not practical, but interesting ideas
still coming).

In 2013, people started talking about verifiable computation.

### Distributed Systems: Understanding Trust

Because systems are decentralized, they have to reason about trust and justify
it using proofs.

- "Principals": people, machines, programs, services, ...
- "Accountability": a principal says a statement
- "Trust": principal A speaks for principal B
  Alice trusts bob@microsoft.com who has key31534 who gives access to file foo.

### Does it actually work? Assurance

Basic strategy: keep it simple, minimize the trusted computing base.

Ideally: verification. Proof that a system satisfies its security spec.

Usually verification is too hard, so you *certify* instead. However, this often
means following a complex process.

Defense in depth (aka bandaids for bugs :) ). This gives no guarantees, but at
least the bad guy has to work harder. Make it more difficult to break into your
system than to break in somebody else's system.

### Configuration (policy)

Even if the code is all correct, the configuration might still be wrong. It's a
serious problem, because every system needs its own config.


What has worked (i.e., gotten wide adoption) and what hasn't
-----

VMs
SSL
Passwords
Safe languages
Firewalls
Process  - SDL


Secure systems (we don't have those)
Capabilities
Metrics for security
MLS/Orange book
User education
Intrusion detection


Why don't we have "real" security
----------------------------------

- People don't buy it.
  Danger is small, it's OK to buy features instead.
  It's expensive
  It's a PITA

- Systems are complicated, so they have bugs


What's next?
------------

- Lower aspirations: In the real world, good security is a bank vault (i.e.,
  used rather rarely)
- Access control doesn't work (it's job is to say "no", and people don't like
  this)
- Retroactive security
  Burglers are stopped by the fear of going to jail, not by security measures
  Financial transactions can be undone.
