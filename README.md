# Tutuorial on Spike 
==================

Contributor: Po-wei Huang

Tutorial on Spike Internal
==================
*   Prerequisite[#pre]
*   Top Level View[#Top]
    *   [What they model?](#model_top)
    *   [Spike's source code](#source_top)
*   Memory System Overview
    *   [What they model?](#model_memory)
*   TLB & MMU
    *   [Spike's source code?](#source_MMU_TLB)
*   Cache simulation
    *   [Spike's source code?](#source_cache)
*   Processor Overview
    *   [What to model?](#model_processor)
*   Hart modeling
    *   [What to model](#model_hart)
    *   [Spike's implementation](#source_hart)
*   Trap modeling
    *   [What to model](#model_trap)
    *   [Spike's implementation](#source_trap)
*   Interrupt modeling
*   Exception modeling
*   Bus and Miscellaneous devices
    *   [Device Simulation](#model_device)
*   Appendix
    *   [Dealing with Instructions](#instrunctions)
<h2 id="pre">Prerequisite</h2>
	Doxygen for large scale C++ program tracing 
	Moreover, this tutorial is for branch debug-0.13, but most of them should be the same.
<h2 id="Top">Top Level View</h2>
<h3 id="modtopel_">What they model?</h3>
  For spike, they use a multi-core framework. Each core includes a MMU for virtual memory, and all of the core have a common I$ and D$. Then, both I$ and D$ connect to a single L2$. The main memory follows. 
  The cores and the memory hierarchy are inside a class sim, and the class could interact with outside by interactive command. Moreover, the sim includes  bus, debug module, boot rom, and real time clock (RTC) . The processors, boot ROM, debug module and RTC are hooked on the bus, but the memory is not. These components together enable spike to run a simple proxy kernel pk.

