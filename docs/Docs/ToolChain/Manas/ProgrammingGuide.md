# Programming Guide

## Basics

### General Guide

Manas is an assembler for SiLago platform (DRRA+DiMArch). It accepts defination of instructions and their dependencies and generates a RTL testbench for simulation. Manas uses the scheduling engine of Vesyla to schedule these instructions based on user-defined dependencies.

The input file of Manas is a plan text file with two segments -- the code segment and the dependency segment.

### Code Segment

Instructions are defined in code segment. A code segment starts with segment identifier:

```
.CODE
```

All statements after the code segment identifer and before any other segment identifier are considered part of the code segment.

A code segment can include multiple sections leading by a cell location identifier:

```
CELL <r, c>
```

It defines the coordinate in terms of row (`r`) and column (`c`) of each instructions that follow it. It applies to all instructions until another cell location identifier or the end of the code segment.

An instruction consists a instruction name and an argument list. They are seperated by space. All arguments should be integers unless specified otherwise. There might be pre-defined macros for specific arguments. You can find all supported instructions in the [Instruction Set](../InstructionSet).
 
!!! Example
	This is a example for code segment

	```
	.CODE
	CELL <0,0>
	SWB REFI 0 3 LATA 0 2
	SWB REFI 0 2 LATA 0 3
	SWB LATA 0 0 REFI 0 1
	REFI_1 3 0 0 0 0 0 0 2
	REFI_1 2 0 0 10 0 0 0 1
	DPU 10 0 3 3 1 0 0 0
	REFI_1 1 0 0 20 0 0 0 0
	```

### Dependency Segment

Dependencies among instructions are defined in dependency segment. A dependency segment starts with segment identifier:

```
.DEPENDENCY
```

Each instruction of DRRA has 5 phases: FETCH, ISSUE, ARRIVE, ACTIVE, END. They are represented by number: 0 to 4 respectively.

User can define dependencies among instruction phases. These dependencies will guide the scheduler to order instructions properly.

Each dependency has 10 fields:

* SRC_ROW: the row of source instruction.
* SRC_COL: the column of source instruction.
* SRC_ID: the id (index) of source instruction.
* SRC_PHASE: the phase of source instruction.
* DST_ROW: the row of destination instruction.
* DST_COL: the column of destination instruction.
* DST_ID: the id (index) of destination instruction.
* SDST_PHASE: the phase of destination instruction.
* D_LO: the lower bound of distance.
* D_HI: the higher bound of distance.

!!! Example
	This is a example for dependency segment

	```
	.DEPENDENCY
	0 0 0 0 0 0 0 1 1 +INF
	0 0 1 1 0 0 1 3 0 +INF
	1 0 1 1 1 0 1 1 2 5
	```