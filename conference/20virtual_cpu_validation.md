Virtual CPU Validation
======================

Question: a server freezes once a month. Why?

Today, 75% of x86 server workloads are virtualized. (Gartner, 2015) Bugs in
hypervisors have become a real problem. CPU virtualization is a hard thing to
do, it happens in Kernel space, ...

Existing solutions:

- Micro-hypervisors with a smaller TCB (well, they can only be *that* small)
- Formal verification (hard, because there is no formal model of CPUs)
- Fuzzing (hard without knowledge of CPU semantics)

This work exploits the fact that a physical and virtual CPU should behave
similarly. Thus, we could adapt and re-use the CPU vendor's testing
infrastructure to test hypervisors.


Intel's architecture
--------------------

Works a bit like CSmith. It generates random tests from a template and some
random code. Each test is a memory image which is then being run. The result is
compared to the same test executed by a reference platform.

Benefits:

- High coverage due to inside knowledge and very high effort put in by Intel.
- Low false positives because tests avoid undefined or nondeterministic
  instructions (?)
- Tests are easy to debug, detailed traces of expected execution can be
  generated.


Adapting the test suite
-----------------------

The Xen hypervisor does not support some virtualization features that tests
depended on.

Some of these were implemented by the authors, others were worked around (e.g.,
KVM does not support data breakpoints).

Testing:

- load tests using hypervisor monitor protocol
- emulate test device for IO instructions

The authors need to take special measurements to avoid OS jitter, and needed to
improve some of the debug tools. It took to weeks to run the first empty test,
and 1.5 extra months to get a real test to pass.

Generating a test takes about 5 seconds, and executing it takes about a second.
Manual debugging of failures takes ~3h per bug (with high variance).


Results
-------

Found 117 bugs. 62% of these were in the instruction emulator.

The instruction emulator is needed even with hardware virtualization, for a few
reasons:

- Port IO or memory-mapped IO
- To support old hardware

Interestingly, the hypervisor can often be tricked into emulating instructions
that it would otherwise execute directly on the hardware.

Most of these bugs (78%) were violations of the CPU spec. 7% of bugs were due to
unclear documentation, and 15% by coding errors.

Six bugs were security vulnerabilities: 3 host DoS attacks. 2 VM DoS attacks,
and 1 privilege escalation.

### Hardware flaws

In addition to bugs, the work found four CPU architecture flaws. These were
situations that violated Popek/Goldberg. Example:

CPU updates two internal registers when a floating-point instruction faults.
Unprivileged instructions can read these registers. However, hypervisors cannot
write these registers, which leads to inconsistencies.
