Sibylfs.io
==========

At some point, the author wanted to write a file system...

... soon, there were questions: what happens if you rename a file that doesn't
exist? What happens if you copy a file onto a directory?

Writing test scripts for each of these cases became impossible. The author
wanted a spec.

Why not POSIX? POSIX is pretty precise. It's written in English, but words like
"shall" or "undefined" mean something very particular.

Some things cut across pages in the POSIX spec. For example, symlinks affect a
number of file system calls. There are also some errors in POSIX. It's somewhat
hard to understand.

In addition to POSIX, we have man pages, libc docs, bug reports, StackOverflow,
the source code...

It's hard to know what file systems are *supposed* to do, and what they
*actually* do, and there are just too many combinations of libc/OS/FS, ...


What is Sibylfs?
----------------

It's a file system specification, and a test oracle to test real-world
implementations.

Sibylfs is a spec that is:

- precise and unambiguous
- maintainable, comprehensive and detailed
- has variants for POSIX, Linux, Mac and FreeBSD
- formalized: a machine can understand it

Sibylfs is also a test oracle:

- Takes as input a trace (at the libc function call level)
- Returns a result that says whether the trace is OK, and what is wrong, if
  anything.

Unlike test suites, no test code writing is needed. Sibylfs only needs collected
traces. Checking traces is fast. These advantages allow combinatorial testing
across a wide variety of systems.


Sibylfs combinatorial testing
-----------------------------

Test generator generates test scripts. These are given to an executor and
replayed on a real file system. The resulting traces are fed to the sibylfs
checker.

Test results:

- Error codes are often non-POSIX conforming.
  Sibylfs tells you exactly how different they are.
- Path resolution is highly variable, especially if paths end in a slash.
- OpenZFS on OS/X leads to process hanging with 100% CPU usage :)

Sibylfs is accurate: it accepts almost 100% of the traces on Linux/ext4 (meaning
that it models that system precisely). Because the spec is so good, we can do a
lot of testing, and things can only improve at this point.


Constructing the spec
---------------------

Start with an initial spec, run it on the platform of interest, and then analyze
the traces by hand.

This takes a long time and is boring.

Yet, now the results are captured in the spec, and formalized.

Another pain point is keeping the spec general enough. It should match all the
traces, but it should also not be just a list of special cases. This currently
takes mostly human ingenuity.

The spec must also be efficient to evaluate... this isn't easy and requires
manual work.


What is it useful for?
----------------------

Testing systems like FSCQ

Useful for other work, e.g., the "scalable commutativity rule" (where they had a
model in symbolic Python, but people weren't all that sure that the model was
correct).
