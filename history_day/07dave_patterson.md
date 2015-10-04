The past and future of hardware architecture
============================================

Speaker: David Patterson


History
-------

In the early 60s, IBM had four incompatible computer lines. Then, the IBM 360
ISA came and hid the architectural differences.

Control path vs datapath. Processor designs split the two. The data path had a
different width than the control path.

Moore's law allowed more complex ISAs (VAX in the 70s)

Intel i432: goal was to invent the next great ISA, because it would probably
stay with them forever. But the design was late.

So, in 10 weeks Gordon Moore (moore's law) designed the 8086. Intel used it
because it was on time (and Motorola 68000 was late).

Analyzign microcoded machines: John Cocke did an experiments where they only
used simple load/store instructions (and ignored all others). Result: software
ran faster!

David Patterson did a 1979 sabbatical at DEC. Explored field-repairable chips.
This lead to the idea of RISC.

- fast instruction cache
- simple instructions could be faster

CISC vs RISC today:

- Hardware today translates x86 into internal RISC
- In the post-PC era, die area and energy are valued, too. 98% of mobile/cloud
  processors are RISCs.

VLIW: very long instruction word: multiple operations in one instruction.
Compiler had all the responsibilities. It would e.g., unroll loops so that it
could schedule more instructions and work around the latencies.

Intel Itanium embraced that, build EPIC (explicitly parallel instruction
computing). Unfortunately, it was an epic failure... Unpredictable branches,
unpredictable cache misses were hard to compile for. The approach was supposed
to be terrific, but the compilers were just too hard to write.

2000s: how can we build scalable multiprocessors? Two schools of thought:

- NUMA with consistency
- Message passing clusters

NUMA is easier to program when communication patterns are complex. Ability to
develop apps using a familiar SMP model. Lower overhead, HW-controlled caching.

People were worried about clusters because they would be hard to administer,
they used the IO-bus and not the memory bus, ...

Cluster advantages: error isolation, easier to repair because machines are
independent, easier to scale, cheaper.

=> Inktomi (and then Amazon, Google, ...) started to rely on clusters of
commodity computers?

Future
------

Moore's law is slowing down.

Number of cores: +18% per year
Prize: -30% per year, used to be -50% in 2000s

Flash will become cheaper than disk next year (!)

3D XPoint technology announced in July 2015:

- non-volatile
- 1000x more resilient than SSD
- 8-10x the density of DRAM

Storage hierarchy gets deeper and more complex. We'll need to design software to
take advantage of this.

Today, agreement that RISC is the right instruction set.

We should have an open ISA!! There is no good technical reason today for this...
There aren't many business reasons for it as well.

Today, companies can offer designs without offering chips (like ARM). Moore's
law is ending => more need for domain-specific stuff.

RISC-V is done at Berkeley, started in 2010. Started as a three-month project to
develop a clean-slate ISA. Surprisingly, people started using it.

Ideas:

- three base integer ISAs, separated by address width (32, 64, 128)
- minimal: <50 base instructions
- optional extensions
  - multiply/divide
  - compressed instructions
- reserve opcodes for custom extensions

Exciting today: it is cheap to build hardware! For 250$, you can get a Zed FPGA
=> working computer with full software stack.

You can even build custom chips: it costs about 30000$ to get 100 custom 28nm
chips.

Check out <http://riscv.org>
