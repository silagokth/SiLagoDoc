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
LABEL INSTR_NAME NAME_0 = VALUE_0, NAME_1 = VALUE_1, ...
```
 
!!! Example
	This is a example for code segment

	```
	.CODE
	CELL <0,0>
	"label_0" REFI port_no=2, init_addr=16
	"label_1" REFI port_no=0
	"label_2" DPU mode=10
	```

### RELATION Segment

Relation segment specifies the hierarchical structure of the mapping. The entire mapping is organized as a hierarichical graph. The outer most node is called ``ROOT``. Inside the ``ROOT`` node, there are other nodes, such as loop node, branch node, and other normal instruction node.

Each node represents either an hierarchical structure or an instruction phase. For example, a node can represent the whole loop structure and it contains all instructions and inner loops inside its children graph. Each instruction in the CODE segment can be divided into multiple instruction phases. Each phase marks a critical timing point when the instruction changes behavior. For example, a ``REFI`` instruction has four phases: FETCH, ISSUE, ACTIVE, and END. The name of the node that represents an instruction phase is to combine the instruction label and the phase index. For example, if a `REFI` instruction has a label called `instr_refi_0`, then the FETCH phase will be mapped to the node named as `instr_refi_0_0` since the FETCH phase is the first phase of the REFI instruction. See [Instruction Phase](../InstructionPhase)

Each relation record in the RELATION segment specifies the children of one hierarchical node. The format is:

````
"NODE_NAME" 1 IS_BULK [NODE0_IN_CHILD0, NODE1_IN_CHILD0, ...] [NODE0_IN_CHILD1, NODE1_IN_CHILD1, ...] EXPAND_LOOP
````

The NODE_NAME can be any string, it's the name of the hierarchical node. The second parameter is deprecated, it's always 1. The third parameter IS_BULK specifies whether this node should be treated as bulk node. A bulk node does not allow overlap with other nodes. The array of child0 and child1 are used to specify the children node in the 0th and 1th child graph. All hierarchical node other than IF-ELSE branch has only child0. The brach node has two children, child0 is the true branch, and child1 is the false branch. The last parameter is the EXPAND_LOOP. It's a flag to suggest the assembler to do cross-iteration software pipelining.

!!! Note
    The IS_BULK and EXPAND_LOOP are very confusing in manas. We will modify manas to make it more clear. Right now, all nodes other than loop should set both parameters to 0. For most inner loop, you should let IS_BULK=0 and EXPAND_LOOP=1. For other loops, set IS_BULK=1 and EXPAND_LOOP=1.

When write node names in child graph array, you can use regular expression to include a group of nodes. For example, `instr_refi_0_.*` will include all the phases of the instruction named as "instr_refi_0".

!!! Example
	This is a example for relation segment

	```
    .CODE
    CELL <0,0>
    "instr0" ROUTE horizontal_dir=0, horizontal_hops=0, vertical_dir=1, vertical_hops=1, direction=0, select_drra_row=0
    "instr1" SWB src_row=0, src_block=0, src_port=0, hb_index=2, send_to_other_row=0, v_index=2
    "instr2" SWB src_row=0, src_block=0, src_port=0, hb_index=3, send_to_other_row=0, v_index=3
    "instr3" SWB src_row=0, src_block=0, src_port=1, hb_index=2, send_to_other_row=0, v_index=4
    "instr4" SWB src_row=0, src_block=0, src_port=1, hb_index=3, send_to_other_row=0, v_index=5
    "instr5" LOOP extra=1, loopid=0, iter=2, step=4
    "instr6" LOOP loopid=1, iter=3, step=2
    "instr7" RACCU mode=1, operand1_sd=1, operand1=14, operand2_sd=1, operand2=15, result=0 
    "instr8" SRAM rw=0, init_addr=0, l1_iter=3, l1_step=1, l1_delay=0, init_addr_sd=1, hops=0
    "instr9" REFI port_no=0, extra=2, init_addr_sd=0, init_addr=0, l1_iter=3, l1_step=1, l1_delay=0, dimarch=1
    "instr10" REFI port_no=2, extra=2, init_addr=0,  l1_iter=3, l1_step=1, l2_delay=2, l2_iter=28, l2_step=1 
    "instr11" REFI port_no=3, extra=2, init_addr=32, l1_iter=3, l1_step=1, l2_delay=2, l2_iter=28, l2_step=1
    "instr12" LOOP loopid=2, iter=29, step=1
    "instr13" DPU mode=10, control=2
    
    CELL <0,1>
    "instr14" DPU mode=1, control=2 
    "instr15" ROUTE horizontal_dir=0, horizontal_hops=0, vertical_dir=1, vertical_hops=1, direction=0
    "instr16" SWB src_row=0, src_block=1, src_port=0, hb_index=1, v_index=2 
    "instr17" SWB src_row=0, src_block=1, src_port=1, hb_index=1, v_index=3
    "instr18" SRAM l1_iter=1, l1_step=1, l1_delay=0, l2_iter=1, l2_step=2, l2_delay=0, hops=0
    "instr19" REFI port_no=0, extra=2, l1_iter=1, l1_step=1, dimarch=1
    "instr20" LOOP extra=1, loopid=0, iter=2, step=4
    "instr21" LOOP extra=0, loopid=1, iter=3, step=2
    "instr22" REFI port_no=2, extra=2, init_addr= 0, l1_iter=3, l1_step=1, l2_iter=28, l2_delay=2, l2_step=0 
    "instr23" REFI port_no=3, extra=2, init_addr=16, l1_iter=3, l1_step=1, l2_iter=28, l2_delay=2, l2_step=0 
    "instr24" LOOP loopid=2, iter=29, step=1
    
    CELL <0,2>
    "instr25" DPU mode=1, control=2 
    "instr26" ROUTE horizontal_dir=0, horizontal_hops=0, vertical_dir=1, vertical_hops=1, direction=0
    "instr27" ROUTE horizontal_dir=0, horizontal_hops=0, vertical_dir=1, vertical_hops=1, direction=1 
    "instr28" SWB src_row=0, src_block=0, src_port=1, hb_index=2, v_index=2
    "instr29" SWB src_row=0, src_block=1, src_port=0, hb_index=2, v_index=1
    "instr30" SWB src_row=0, src_block=1, src_port=0, hb_index=1, v_index=3
    "instr31" LOOP extra=1, loopid=0, iter=2, step=4
    "instr32" REFI port_no=3, extra=2, l1_step=1, l1_iter=28, l1_delay=5, l2_iter=2, l2_delay=25, l2_step=0
    "instr33" REFI port_no=1, extra=2, l1_step=1, l1_iter=28, l1_delay=5, l2_iter=2, l2_delay=25, l2_step=0
    "instr34" LOOP extra=0, loopid=1, iter=3, step=2
    "instr35" SRAM rw=0, l1_iter=1, l1_step=1, l1_delay=0, hops=0, init_addr_sd=d, init_addr=14
    "instr36" REFI port_no=0, extra=2, l1_iter=1, l1_step=1, dimarch=1
    "instr37" LOOP loopid=2, iter=29, step=1
    "instr38" SRAM rw=1, l1_iter=1, l1_step=1, l1_delay=0, hops= 0, init_addr_sd=d, init_addr=14
    "instr39" REFI port_no=2, extra=2, l1_iter=1, l1_step=1, dimarch=1

    .RELATION
    "ROOT" 1 0 ["LOOP0","Op_0_0_0_1", "Op_0_0_0_2", "Op_0_0_1_1", "Op_0_0_1_2", "Op_0_0_2_1","Op_0_0_2_2", "Op_0_0_3_1", "Op_0_0_3_2", "Op_0_0_4_1", "Op_0_0_4_2","Op_0_1_0_1", "Op_0_1_0_2", "Op_0_1_0_3", "Op_0_1_1_1", "Op_0_1_1_2","Op_0_1_2_1", "Op_0_1_2_2", "Op_0_1_3_1", "Op_0_1_3_2", "Op_0_1_4_1", "Op_0_1_4_2", "Op_0_1_5_1","Op_0_2_0_1", "Op_0_2_0_2", "Op_0_2_0_3", "Op_0_2_1_1", "Op_0_2_1_2","Op_0_2_2_1", "Op_0_2_2_2", "Op_0_2_3_1", "Op_0_2_3_2", "Op_0_2_4_1", "Op_0_2_4_2","Op_0_2_5_1", "Op_0_2_5_2"] [] 0

    "LOOP0" 1 1 ["LOOP0_BODY","Op_0_0_5_1","Op_0_1_6_1","Op_0_2_6_1"] [] 1

    "LOOP0_BODY" 1 0 ["LOOP1","Op_0_1_4_3", "Op_0_1_4_4", "Op_0_1_5_2", "Op_0_1_5_3","Op_0_2_7_1","Op_0_2_8_1"] [] 0

    "LOOP1" 1 1 ["LOOP1_BODY","Op_0_0_6_1","Op_0_1_7_1","Op_0_2_9_1"] [] 1

    "LOOP1_BODY" 1 0 ["LOOP2","Op_0_0_7_1", "Op_0_0_8_1", "Op_0_0_8_2", "Op_0_0_8_3", "Op_0_0_8_4", "Op_0_0_10_1", "Op_0_0_11_1","Op_0_0_9_1", "Op_0_0_9_2", "Op_0_0_9_3","Op_0_1_8_1", "Op_0_1_9_1", "Op_0_2_10_1", "Op_0_2_10_2", "Op_0_2_10_3", "Op_0_2_10_4","Op_0_2_11_1","Op_0_2_11_2", "Op_0_2_11_3","Op_0_2_13_1","Op_0_2_13_2","Op_0_2_13_3", "Op_0_2_13_4", "Op_0_2_14_1", "Op_0_2_14_2","Op_0_2_14_3"] [] 0

    "LOOP2" 1 0 ["LOOP2_BODY", "Op_0_0_12_1","Op_0_1_10_1", "Op_0_2_12_1"] [] 1

    "LOOP2_BODY" 1 0 ["Op_0_0_13_1", "Op_0_0_13_2", "Op_0_0_13_3", "Op_0_0_10_2", "Op_0_0_10_3", "Op_0_0_11_2","Op_0_0_11_3", "Op_0_1_8_2", "Op_0_1_8_3",  "Op_0_1_9_2", "Op_0_1_9_3","Op_0_2_7_2","Op_0_2_7_3", "Op_0_2_8_2","Op_0_2_8_3"] [] 0
    
	```

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
