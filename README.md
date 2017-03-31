# Tutuorial on Spike 


Contributor: Po-wei Huang

Tutorial on Spike Internal
==================
*   [Prerequisite](#pre)
*   [Top Level View](#Top)
    *   [What they model?](#model_top)
    *   [Spike's source code](#source_top)
*   [Memory System Overview](#Memory)
    *   [What they model?](#model_memory)
*   [TLB & MMU](#MMU_TLB)
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
<h3 id="model_top">What they model?</h3>
For spike, they use a multi-core framework. Each core includes a MMU for virtual memory, and all of the core have a common I$ and D$. Then, both I$ and D$ connect to a single L2$. The main memory follows.  
  

The cores and the memory hierarchy are inside a class sim, and the class could interact with outside by interactive command. Moreover, the sim includes  bus, debug module, boot rom, and real time clock (RTC) . The processors, boot ROM, debug module and RTC are hooked on the bus, but the memory is not. These components together enable spike to run a simple proxy kernel pk.  
  
![Top level overview](./pictures/Sim.png)  
<h3 id="source_top">Spike's source code</h3>
	The code below comes from riscv-isa-sim/spike_main/spike.cc. You could see that I$ and D$ connect to L2$ by miss handler. Moreover, for each core, it has a mmu and the mmu connect to a single ic and dc.  
After all the components are connected, the method run is called to start the simulation.  

![Source of Top](./pictures/Spike_main.png)  
	On the other hand, inside riscv-isa-sim/riscv/sim.cc, you could see many bus.add_device(), just like the following figure shows. Spike use this function to attach device on bus. After these attachments are done, spike could start to run.  
	
![Source of Bus add](./pictures/Bus_Add_device.png)  

<h2 id="Memory">Memory system overview</h2>
<h3 id="model_memory">What they model?</h3>  

![Memory system overview](./pictures/Memory_system.png)  

The picture above is an overview of the memory system. The MMU contains a TLB, which could send back the data without invocation of cache. If the TLB fail, they will go through the table and access the cache. For cache, they model a write-back cache, and use sets/ways/line size to set the configuration. This scheme actually will make cache simulation inaccurate, but they do this in order to speed up performance of simulator.  

<h2 id="MMU_TLB">TLB & MMU</h2>
<h3 id="source_mmu">Spike's source code</h3>
When an instruction execute a load, it will call load function of MMU and use WRITE_RD to write the data back t register. Then, how to implement the MMU load?  
![Instruction load](./pictures/Instruction_MMU.png) 
Below is an excerpt of riscv-isa-sim/riscv/mmu.h. The functions are defined in macro. The load will go through TLB first and then go to the slow path if TLB miss happens.  
![MMU trace](./pictures/mmu_trace.png) 
Then, when TLB fail, MMU will call the slow path, and it will ask tracer to call trace. The trace will start to access the cache. Finally, when we jump to riscv-isa-sim/riscv/cachesim.h, we could see that the tracer will call access function of cache. 
![cache trace](./pictures/cache_trace.png)
To understand how the cache is accessed, we could see riscv-isa-sim/riscv/cachesim.cc shown below. There are two functions, access and victimize. When tracer calls trace, the cache will call access.The access will check tag and then do the write or read. Moreover, it use lfsr to find the victim when a replacement happens.
![cache access](./pictures/Cache_code.png)




