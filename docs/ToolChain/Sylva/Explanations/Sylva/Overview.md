# Sylva Overview

Sylva is a tool for application-level synthesis (ALS). It accept a synchronous dataflow (SDF) graph as input and generates a solution after the DSE process. Currently, Sylva generate a solution based on idealized hardware. Further assembly process need to be implemented to automatically finalize the solution.

## Input Files and Data Structures

See [Sylva Input Format](../../Reference/Input.md) for the input files and data structures used in Sylva.

## DSE Process

1. [**Binding**](Bind.md)
2. [**Placement**](Place.md)
3. [**Routing**](Route.md)
4. [**NoC Wire Synthesis**](Noc.md)
5. [**GLIC Synthesis**](Glic.md)
6. **Full System Functional Simulation**

## Assembly Process

1. **Memory Synthesis**
2. **Transporter Instruction Generation**
3. **NoC Synthesis**
4. **GRLS Protocol Synthesis**
5. **Control Synthesis**
6. **DRAM Interface**
7. **Cost Metrics Estimation**
8. **Full System Simulation and Validation**
