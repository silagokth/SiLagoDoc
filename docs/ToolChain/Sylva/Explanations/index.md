# Sylva Overview

Sylva is a tool for application-level synthesis (ALS). It accepts a synchronous dataflow (SDF) graph as input and generates a solution after the DSE process. Currently, Sylva generate a solution based on idealized hardware. Further assembly process needs to be implemented to automatically finalize the solution.

One of the essential inputs of Sylva is an HLS library from Vesyla tool, as it provides information about the dimensions, estimated energy consumption, and data transfer patterns of each algorithm used in the target application. Sylva uses this information to explore the design space of the target application and produces the final design through the assembly process. For this task, the ALS tool can be divided into two parts: Design Space Exploration (DSE) and Assembly (ASE).


## Input Files and Data Structures

See [Sylva Input Format](../Reference/Input.md) for the input files and data structures used in Sylva.

## DSE Process

The first step of the DSE process is to select instances of a set of target algorithms by evaluating information such as area and energy. A set of algorithm instances that meet the given constraints will then be assigned specific locations on the chip canvas. This step must take into account the potential routing difficulty by reserving extra space if necessary. Once the decisions about the instances’ locations are made, the connections can be drawn using heuristic algorithms such as A* algorithm. Finally, an optimization problem is created and solved to build the schedules for static data transport between instances. The DSE solution can optionally be verified by the GLIC simulator to ensure that the design from DSE is functional.

1. [**Binding**](DSE-Bind.md)
2. [**Placement**](DSE-Place.md)
3. [**Routing**](DSE-Route.md)
4. [**NoC Wire Synthesis**](DSE-Noc.md)
5. [**GLIC Synthesis**](DSE-Glic.md)
6. [**Full System Functional Simulation**](DSE-Sim.md)

## Assembly Process

Given the binary output from the DSE phase, the ASE phase begins by synthesizing actual hardware memories for data communication. It then reruns the routing algorithms with a legalization method to ensure that the lengths after rerouting remain approximately the same as before. It then resynthesize the NoC with the fixed delay method. After that, it compiles the instructions needed for the hardware modules that move data between locations. The other steps are on-going processes with an objective to finalize the output format in aspect of hardware. There is also a need for the full system simulation and validation in the RTL-level.

1. [**Memory Synthesis**](ASM-Mem.md)
2. [**Re-routing**](ASM-Route.md)
3. [**NoC Resynthesis**](ASM-Noc.md)
2. [**Transporter Instruction Generation**](ASM-Transp.md)
4. **GRLS Protocol Synthesis**
5. **Control Synthesis**
6. **DRAM Interface**
7. **Cost Metrics Estimation**
8. **Full System Simulation and Validation**
