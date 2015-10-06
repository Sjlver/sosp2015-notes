Yesquel
=======

There are dozens of (incompatible) NoSQL systems today.

NoSQL is not a feature :)

What NoSQL wants, is a system that is scalable and distributed, nimble and cheap
to run.

Yesquel provides these advantages, but supports full SQL.

SQL is cool:

- indexes, transactions, ...
- rich set of data types
- universal and compatible
- everybody knows how to use it


How Yesquel works
-----------------

DBMS consists of a query processor and a storage engine.

- Query processor plans and optimizes a query
- Storage engine manages data layout, fetches records, ...

Yesquel architecture:

- Clients run on separate hosts
- Each client gets its own query processor (running on the same host). This
  processor is a library linked to the app.
- Client machines are connected to *storage servers* by a fast network. All
  storage servers are one logical key-value store.
- Clients also run a "node storage client library" to access the storage
  servers.

Yesquel has three logical layers: query processor, storage engine, and the
node distributed storage system. Transactions are provided at the lowest level
(node storage system).

### Transaction ideas:

- commits by client (idea from synfonia, SOSP 2007)
- multi-version concurrency control
- local clocks provide timestamps that are used while accounting for drift
- use commutative operations when we can

### Yesquel storage engine

Yesquel stores data in *ordered maps* (sets of key-value pairs with support for
ordered enumeration).

It uses an ordered map to store the table (ordered by primary key). An
additional ordered map is used for each secondary index (mapping it to the
primary key).

Ordered maps are implemented as distributed balanced trees. It's a tree where
each node is stored on a different server. Yesquel adds:

- caching with back-down searches
- split nodes based on load (not just based on size)
- delegated splits (prevents contention if multiple clients try to split a node)
- right splits (allows adding keys during splits)


Cool demo! Note to self: this was the fourth or so talk with a demo, and they
were always well performed. Looking through the audience showed that this was
when they woke up... => Let's do demos in my talk.

Takeaway: Yesquel gives SQL convenience, and manages to map it to *few read
operations* on storage servers (thus also giving performance).

Evaluation indeed shows performance comparable to Redis, while handling the kind
of SQL queries that Wikipedia would perform to load a page.
