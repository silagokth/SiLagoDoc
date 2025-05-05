# Hazard

Here we only talk about data hazards.

## Standard Hazards

A data hazard is created whenever there is a dependence between data read and/or write operations. Without such dependency information perserved by some proper format, the data access order might be different from the intended order expressed by the programmer in source code thus might lead to unintended output. The goal of harzard detection is to exploit parallesism by perserving data access order only where it affects the outcome of the program. Data hazard can be categorized as Read-After-Write (RAW), Write-After-Write (WAW) and Write-After-Read (WAR). For out-of-order issue hardware or compiler that exploits instruction execution order all three types of hazard can happen. Therefore, we need to preserve the dependency information in order to avoid those hazards.

## Hazards in Vector Machine and Vesyla

In vesyla, the case for RAW is simple. It will be directly absorbed by the data dependency edge that is anyway created. WAR and WAW, requires creation of additional special edges to indicate that there are data dependencies. Data hazards for vector variables are different from those for scalar variables. For example, in the following figure, a WAW dependency is not necessary for scalar variables because the first “Write” operation can’t affect the final result of that scalar variable hence can be directly removed. While for vector variables, WAW dependency is absolutely necessary, because those two “Write” operations might write to different part of the vector therefore both will influence the final result of the vector variable.

<!-- ![Hazard Example](hazard_example.png) -->

Different from scalar machine, vector machine need extra information to create dependencies. Vector operations usually last for some time period. The dependencies between vector operations need to specify which timing point they are referring to. Specifically, the start and end time of a vector operation are important. Regarding just start and end time of vector operations, we can create 4 types of dependencies:

1. Dependency between the start of predicessor and the start of successor.
2. Dependency between the start of predicessor and the end of successor.
3. Dependency between the end of predicessor and the start of successor.
4. Dependency between the end of predicessor and the end of successor.

Data dependency analysis technique can be found in section [Dependency Analysis](./DependencyAnalysis.md).
