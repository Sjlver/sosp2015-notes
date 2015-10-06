Drowsy Power Management
=======================

Motivation: smart phones today switch on whenever they have work to perform.
During a wakeup, the some interrupt causes the Kernel to do work. Then the event
is handled. Wake locks ensure the system remains on for the duration of the
entire event. After the event, the system performs some work to transition back
to suspend.

The transitions are expensive! They take up ~75% of the energy needed to process
the actual event.

Things that take a lot of energy:

- freezing all tasks
- suspending all devices

The key idea of Drowsy is to wake up only what is necessary.


Example: pulling data from Wifi
-------------------------------

Needs the app, some system services, the Wifi and alarm chip.

*Does not need* the USB controller, the GSM chip, ... Android nevertheless wakes
these up.

Drowsy automatically constructs a *minimal wake set* of tasks and devices. These
are the smallest sets that produce correct behavior. They are expanded
on-demand.


Constructing wake sets
----------------------

The initial wake set contains previously running tasks.

If tasks wake up other tasks blocked on IO, Drowsy adds those tasks to the wake
set. Once tasks do IO, the devices are woken up from suspended state.

For the "pulling data from Wifi" example, drowsy wakes 16 tasks and 19 devices.
This is around 2% of what would otherwise be woken up.


Instrumenting Android
---------------------

Drowsy substitutes function pointers in device drivers in order to wrap the
functionality. This means it can wake up the device before any code of the
actual driver runs.


Evaluation
----------

Benchmark app that wakes up periodically and handles various events (push
notification, network request, sample accelerator data...)

Nexus 6 device connected to a power meter for data measurement.

Use notification LED on the phone (and a photosensor) to sync events between the
phone software and the measurement system.

Drowsy achieves 1.5 to 5x better power efficiency. Usually, it also achieves a
speedup (!) because less devices need to be woken up. Event handling itself
takes a bit longer because devices are woken up on demand, but the overall
completion time is less.

<http://www.cs.umd.edu/projects/drowsy/>
