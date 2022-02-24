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

Each instruction has multiple fields. Some fields are controllable which means one can set the value of that field through the assembly language. All the controllable fields are marked by **bold text** in the [Instruction Set](../InstructionSet).

Each field in an instruction has a default value. In the Instruction Set page, the default value is explicitly written in the **Range/Value** section unless the default value is 0. While wring the Assebly language, you only need to set the field whoes expected value is not the default value.

To write a instruction, use the following format:

```
INSTR_NAME NAME_0 = VALUE_0, NAME_1 = VALUE_1, ...
```
 
!!! Example
	This is a example for code segment

	```
	.CODE
	CELL <0,0>
	REFI port_no=2, init_addr=16
	REFI port_no=0
	DPU mode=10
	```

### RELATION Segment

!!! Warning
	Not complete

### DEPENDENCY Segment

Dependencies among instructions are defined in dependency segment. A dependency segment starts with segment identifier:

```
.DEPENDENCY
```

Each instruction of DRRA has 5 phases: FETCH, ISSUE, ARRIVE, ACTIVE, END. They are represented by number: 1 to 5 respectively.

User can define dependencies among instruction phases. These dependencies will guide the scheduler to order instructions properly. Each phase corresponds to a vertex in the dependency graph. You should know the naming convension when reference those phases. For example, the 2nd phase of the 1st instruction in cell <3,5> will be mapped to a vertex called **Op_3_5_0_2**.

A dependency is defined by four fields:

* SRC : the source vertex name.
* DEST: the destination vertex name.
* D_LO: the lower bound of distance.
* D_HI: the higher bound of distance.

The format is:

```
SRC DEST D_LO D_HI
```

!!! Example
	This is a example for dependency segment

	```
	.DEPENDENCY
	"0_0_0_0" "0_0_1_1" 1 +INF
	"0_0_1_1" "0_0_1_3" 0 +INF
	"1_0_1_1" "1_0_1_1" 2 5
	```
