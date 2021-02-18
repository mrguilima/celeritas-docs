<!--
Generate to latex:
```
$ pandoc -o code-arch.tex -r gfm code-arch.md
```
-->
## Code architecture

In the short term, Celeritas is designed as a standalone application that
transport particles exclusively on device. To support robust and rapid unit
testing, its components are designed to run natively in C++ on traditional
CPUs regardless of whether CUDA is available for on-device execution.

Like other GPU-enabled Monte Carlo transport codes such as Shift, the low-level
component code used by transport kernels is designed so that each particle
track corresponds to a single thread, since particle tracks once created are
independent of each other. There is therefore essentially no cooperation
between individual threads, facilitating the dual host/device annotation of
most of Celeritas. The allocation of secondary particles and the initialization
of new tracks from these secondaries both require CUDA-specific programming,
but those components are encapsulated so that both host and device code
can safely construct secondaries.

To support parallelizing our initial development over several team members, and
to facilitate refactoring and performance testing of code, Celeritas uses a
highly modular programming approach based on composition rather than
inheritance. As much as possible, each major code component is built of
numerous smaller components and interfaces with as few other components as
possible. The interfaces with other components are furthermore