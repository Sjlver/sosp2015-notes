Virtualization
==============

Speaker: Andrew Herbert (Cambridge)
Sunday 13:00


Definition: Illusion of *efficiently* running multiple independent computers
known as virtual machines.

- VMM or Hypervisor provides this illusion.
- The abstraction provided is that of a complete computer in its own right.

Historically, emulated a real machine as precisely as possible. These days, the
abstraction is looser.


Virtual machine monitors
------------------------

Origins in 1965, on the IBM M44/44X to explore page replacement algorithms. The
system had multiple "virtual machines" with their own paging strategies.

Evolved into the VM/370. Addressed three needs:

- Time-sharing the computer.
- Running legacy applications and their OSs alongside new applications.
- Developing new OS versions using the same time-sharing technology as for
  applications.

VM/370 structure:

- Control program at the bottom. Did paging, virtual disks and device allocation.

- On top ran multiple CMS OSs (conversational monitor system). One VM ran the
  "remote spooling and communication service". Other VMs could run other versions
  of the OS.

Formalization of virtualization: Popek and Goldberg (1974) defined "sensitive
instructions". They stated that for pure virtualization, all sensitive
instructions need to be privileged (i.e., trap).

Between 1975 and 1995, nothing much happened in the virtualization space...

### Special effect VMMs

In the 1990s, hypervisors came back:

- Hypervisor-based fault tolerance (Schneider & Bressoud, 1996)
- ReVirt: enabling intrusion analysis (Dunlap et al., 2002)

### Hosted VMMs

The idea that a VMM could run on top of another OS. Solved the OS coexistence
problem. VMware Desktop for Windows came out in 1999.

### Native/Bare Metal VMMs

DISCO (1997): hybrid virtualization with binary rewriting and shadowing (VMM
kept shadow copies of the page table and other data structures) in place of
simulation.

Out of this came VMware ESX Server (2002). It did system-wide resource
multiplexing. Ballooning and content-based page sharing.

Xen (2003) introduced paravirtualization.


Benefits of VMs
---------------

Desktop virtualization:

- OS coexistence
- checkpoint/restore, snapshotting

Server virtualization:

- Strong isolation
- Server consolidation on less hardware
- Statistical multiplexing
- Easier management of resources


Talking points
--------------

- More mileage from virtualization by modifying OS semantics?
- What is the TCB when virtualizing?
- Revisit layered abstract machines
- Prove systems correct by default?
