# Sylva Overview

Sylva is a tool for application-level synthesis (ALS). It accepts a Homogeneous
Synchronous Dataflow (HSDF) graph as input and produces a hardware design after
two phases: **Design Space Exploration (DSE)** and **Assembly (ASM)**. DSE works
on idealised hardware to explore the design space; Assembly turns the chosen
design point into a hardware-realisable description.

One of the essential inputs of Sylva is an HLS library produced by the
[Vesyla](../../Vesyla/index.md) tool. For each algorithm used in the target
application it provides the dimensions, estimated energy, and static data-transfer
patterns of one or more **AlImp** (Algorithm Implementation) variants. Sylva uses
this information to explore the design space and to assemble the application from
these AlImps.

## Architecture

Before the synthesis stages, it helps to understand the target hardware: a network
of AlImps orchestrated by a host system, with output buffers, transporters and
input buffers carrying data between them under a static, cycle-accurate schedule.

- [**Hardware Architecture**](Architecture.md) — AlImp micro-architecture, the
  communication components, the application-level architecture and the
  hardware/software co-design.

## Input Files and Data Structures

See [Sylva Input Format](../Reference/Input.md) for the input files and data
structures used by Sylva.

## DSE Process

DSE first selects an AlImp instance for each algorithm by evaluating area and
energy. The selected instances are then placed on the chip canvas (reserving extra
space for routing where needed), connected with a heuristic path-finder, and given
static communication schedules by solving an optimisation problem. The DSE result
can optionally be verified by the GLIC simulator.

1. [**Binding**](DSE-Bind.md)
2. [**Placement**](DSE-Place.md)
3. [**Routing**](DSE-Route.md)
4. [**NoC Wire Synthesis**](DSE-Noc.md)
5. [**GLIC Synthesis**](DSE-Glic.md)
6. [**Full System Functional Simulation**](DSE-Sim.md)

## Assembly Process

Given the binary output from DSE, Assembly synthesises actual hardware memories
for communication, re-runs routing with a legalisation method (so re-routed
lengths stay close to the originals), resynthesises the NoC with fixed delays, then
generates the code and firmware that make the design executable.

1. [**Memory Synthesis**](ASM-MemSyn.md)
2. [**Re-routing**](ASM-Route.md)
3. [**NoC Resynthesis**](ASM-ReNoc.md)
4. [**TLB Code Generation**](ASM-TLB.md)
5. [**Transporter Code Generation**](ASM-Transporter.md)
6. [**Control Synthesis**](ASM-Control.md)

!!! note "Out of scope (future work)"
    Cost-metric estimation, the DRAM interface and full RTL-level system
    validation are part of the broader road map but are not yet implemented in the
    tool.
