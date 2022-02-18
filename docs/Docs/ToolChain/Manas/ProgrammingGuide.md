# Programming Guide

## Basics

### General Guide

Manas is an assembler for SiLago platform (DRRA+DiMArch). It accepts defination of instructions and their dependencies and generates a RTL testbench for simulation. Manas uses the scheduling engine of Vesyla to schedule these instructions based on user-defined dependencies.

The input file of Manas is a plan text file with four segments:

  - the DATA segment
  - the CODE segment
  - the RELATION segment
  - the DEPENDENCY segment.

### DATA Segment

Data layout in DiMArch and Register Files are defined in DATA segment. A DATA segment starts with a segment identifier:

```
.DATA
```

A variable is defined by the following syntax:

```
VAR_NAME VAR_LOCATION VAR_TYPE ROW COL INIT_DATA
```

VAR_NAME: the name of a variable. It must start with the symbol ``$``. It must be unique.

VAR_LOCATION: the location of the varialbe, either DiMArch (VAR_LOCATION=1) or Register File (VAR_LOCATION=0).

VAR_TYPE: the type of the variable, either integer (VAR_TYPE=0) or fixpoint(VAR_TYPE=1)

ROW & COL: The coordinate of the DiMArch or Register File.

INIT_DATA: The initialization data of the variable. It also defines the variable size. The size must be multiple of 16 for DiMArch variables since each DiMArch row has 16 words. To initialize the variable, one can use any of the following method:

!!! Example
	Example of variable initialization
	
	```
	$A 1 0 0 0 ONES(16)
	$B 1 0 0 0 ZEROS(16)
	$C 1 0 0 0 [1,2,3,4,5,6,7,8,7,6,5,4,3,2,1]
	```

### CODE Segment

Instructions are defined in CODE segment. A CODE segment starts with segment identifier:

```
.CODE
```

All statements after the CODE segment identifer and before any other segment identifier are considered part of the CODE segment.

A code segment can include multiple sections leading by a cell location identifier:

```
CELL <r, c>
```

It defines the coordinate in terms of row (`r`) and column (`c`) of each instructions that follow it. It applies to all instructions until another cell location identifier or the end of the CODE segment.

An instruction consists a instruction name and an argument list. They are seperated by space. All arguments should be integers unless specified otherwise. You can find all supported instructions in the [Instruction Set](../InstructionSet).
 
!!! Example
	This is a example for code segment

	```
	.CODE
	CELL <0,0>
	REFI_1 3 0 0 0 0 0 0 2
	REFI_1 2 0 0 10 0 0 0 1
	DPU 10 0 3 3 1 0 0 0
	REFI_1 1 0 0 20 0 0 0 0
	```

### RELATION Segment

Instructions hierarchy is defined in relation segment.

```
.RELATION
```

### DEPENDENCY Segment

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
