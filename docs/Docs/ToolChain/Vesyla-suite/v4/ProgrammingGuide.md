# Programming Guide

## Introduction

The Proto-Assembly Language is a low-level language that is used to write programs for the DRRA-2 architecture. The language is designed to be simple and easy to understand, while still providing the necessary features to write complex programs. The Proto-Assembly Language are structured assembly language whose instructions are not yet been scheduled. It preserves control hierarchy but lack of timing information. This guide will provide an overview of the language and how to write programs in it. In this guide, you will learn about the syntax of the Proto-Assembly Language, and its constraints.

## File Types

The Proto-Assembly Language uses the `.pasm` file extension for source files. These files contain the source code for a program written in the Proto-Assembly Language.

To supplement the Proto-Assembly Language, the `.cstr` file extension is used for constraint files. These files contain the constraints for the program written in the Proto-Assembly Language. The constraints are necessary for the scheduling of the instructions in the Proto-Assembly Language.

The output of the scheduling and synchronization process is an assembly language file with `.asm` as suffix. This file contains the scheduled instructions that is ready to processed by the assembler.

## Syntax for Proto-Assembly Language

### Comments

Comments in the Proto-Assembly Language are denoted by the `#` character. Anything after the `#` character on a line is considered a comment and is ignored by the compiler.

### Syntax Format

All regions, operations, and instructions follow the same syntax format. The syntax format is as follows:

```proto-assembly
NAME <ID> (PARAM_0=VALUE_0, PARAM_1=VALUE_1, ...) {
    CONTENT_0
    CONTENT_1
    ...
}
```
However, different regions, operations, and instructions requires only some parts of the syntax format. For example, **loop** region usually don't need the `ID` part; Instructions don't have any contents.

The following EBNF grammar describes the syntax of the Proto-Assembly Language:

```ebnf
start: (loop_region | cond_region | epoch_region)*

loop_region: "loop"   ("<" IDENTIFIER ">")? ("(" parameter ("," parameter)* ")") "{" (loop_region | cond_region | epoch_region)+ "}"
cond_region: "cond"   ("<" IDENTIFIER ">")? ("(" parameter ("," parameter)* ")") "{" (loop_region | cond_region | epoch_region)+ "}"
epoch_region: "epoch" ("<" IDENTIFIER ">")? ("(" parameter ("," parameter)* ")")? "{" cell_region+ "}"
cell_region: "cell"   ("<" IDENTIFIER ">")? "(" parameter ("," parameter)* ")" "{" (cop_region | rop_region | raw_region)+ "}"

cop_region: "cop" ("<" IDENTIFIER ">")? ("(" parameter ("," parameter)* ")")? "{" instruction+ "}"
rop_region: "rop" ("<" IDENTIFIER ">")? ("(" parameter ("," parameter)* ")")? "{" instruction+ "}"
raw_region: "raw" ("<" IDENTIFIER ">")? "{" instruction+ "}"

instruction: IDENTIFIER ("<" IDENTIFIER ">")? ("(" parameter ("," parameter)* ")")?
parameter: IDENTIFIER "=" (IDENTIFIER | NUMBER)

IDENTIFIER: /[_a-zA-Z][_a-zA-z0-9]*/
NUMBER: /[+-]?(0[xdob])?[0-9\.]+/

%import common.WS
%ignore WS
COMMENTS: /#.*/
%ignore COMMENTS
```

### Concepts

Regions and instructions are the basic building blocks of a program written in the Proto-Assembly Language. We introduce the following concepts to help you understand the structure of a program written in the Proto-Assembly Language.

- **start** region represents the whole program. It contains one or more regions among **loop**, **cond**, and **epoch**.
- **loop** region is a hierarchical region that contains one or more regions among **loop**, **cond**, and **epoch**.
- **cond** region is a hierarchical region that contains one or more regions among **loop**, **cond**, and **epoch**.
- **epoch** region is a region that contains one or more **cell** region.
- **cell** region is a region that contains one or more operations among **rop**, **cop**, and **raw**.
- **rop** or **resource operation** is a region that contains one or more resource instructions.
- **cop** or **control operation** is a region that contains one or more control instructions.
- **raw** or **raw assembly operation** is a region that contains assembly instruction that do not need to be process by the scheduler.
- **control instructions** are instructions that are executed by a sequencer.
- **resource instructions** are instructions that are executed by a specific resource.

!!! note
    The Proto-Assembly Language is case-sensitive. All keywords must be written in lowercase.

!!! note
    The **raw** region is not compatible with **cop** and **rop** regions. They are not allowed in the same **epoch** region. This is because the instruction scheduling process works on the entire **epoch**.


### Example

The following is an example of a simple program written in the Proto-Assembly Language:

```proto-assembly
epoch <rb0> {
    cell (x=0, y=0) {
        rop <route0r> (slot=0, port=2){
            route (slot=0, option=0, sr=0, source=2, target= 0b010000000)
        }

        rop <input_r> (slot=1, port=0){
            dsu (slot=1, port=0, init_addr=0)
            rep (slot=1, port=0, level=0, iter=2, step=1, delay=0)
        }

        rop <input_w> (slot=1, port=2){
            dsu (slot=1, port=2, init_addr=0)
            rep (slot=1, port=2, iter=2, step=1, delay=0)
        }

        rop <read_ab> (slot=2, port=3){
            dsu (slot=2, port=3, init_addr=0)
            rep (slot=2, port=3, iter=2, step=1, delay=0)
        }
    }

    cell (x=1, y=0) {
        rop <route1wr> (slot=0, port=2){
            route (slot=0, option=0, sr=1, source=1, target= 0b0000000000000110)
            route (slot=0, option=0, sr=0, source=3, target= 0b010000000)
        }

        rop <write_a> (slot=1, port=2){
            dsu (slot=1, port=2, init_addr=0)
            rep (slot=1, port=2, iter=1, step=1, delay=t1)
        }

        rop <write_b> ( slot=2, port=2){
            dsu (slot=2, port=2, init_addr=0)
            rep (slot=2, port=2, iter=1, step=1, delay=t1)
        }

        rop <swb> ( slot=0, port=0){
            swb (slot=0, option=0, channel=4, source=1, target=4)
            swb (slot=0, option=0, channel=5, source=1, target=5)
            swb (slot=0, option=0, channel=3, source=4, target=3)
        }

        rop <read_a_seq> ( slot=1, port=1){
            dsu (slot=1, port=1, init_addr=0)
            rep (slot=1, port=1, iter=16, step=1, delay=0)
        }

        rop <read_b_seq> ( slot=2, port=1){
            dsu (slot=2, port=1, init_addr=0)
            rep (slot=2, port=1, iter=16, step=1, delay=0)
        }

        rop <write_c_seq> ( slot=3, port=0){
            dsu (slot=3, port=0, init_addr=0)
            rep (slot=3, port=0, iter=16, step=1, delay=0)
        }

        rop <compute> ( slot=4, port=0){
            dpu (slot=4, mode=1)
        }

        rop <read_c> ( slot=3, port=3){
            dsu (slot=3, port=3, init_addr=0)
            rep (slot=3, port=3, iter=1, step=1, delay=0)
        }
    }

    cell (x=2, y=0) {
        rop <route2w> ( slot=0, port=2){
            route (slot=0, option=0, sr=1, source=1, target= 0b0000000000000100)
        }

        rop <write_c> ( slot=2, port=2){
            dsu (slot=2, port=2, init_addr=0)
            rep (slot=2, port=2, iter=1, step=1, delay=0)
        }

        rop <output_r> (slot=1, port=3){
            dsu (slot=1, port=3, init_addr=0)
            rep (slot=1, port=3, iter=1, step=1, delay=0)
        }

        rop <output_w> (slot=1, port=1){
            dsu (slot=1, port=1, init_addr=0)
            rep (slot=1, port=1, iter=1, step=1, delay=0)
        }
    }
}
```

## Syntax for Constraint Files

The constraint files are used to specify the constraints for the program written in the Proto-Assembly Language. The constraints are necessary for the scheduling of the instructions in the Proto-Assembly Language.

Constraint is also organized as code region. Only the `epoch` region is supported in the constraint file because only it requires timing constraints. The `ID` field of the `epoch` region should be the same as the `epoch` region in the proto-assembly file, which is used to match the constraints with the program.

### Comments

Comments in the constraint file are denoted by the `#` character. Anything after the `#` character on a line is considered a comment and is ignored by the compiler.

### Syntax Format

The syntax format for the constraint file is as follows:

```constraint
epoch ID {
    TYPE CONTENT_0
    TYPE CONTENT_1
    ...
}
```

Currently, we only support **linear** constraint type. The linear constraints support normal equality and inequality.

The following EBNF grammar describes the syntax of the constraint file:

```ebnf
start: (epoch_region)*
epoch_region: "epoch" ("<" IDENTIFIER ">")? ("(" parameter ("," parameter)* ")")? "{" constraint* "}"
constraint: IDENTIFIER ANY
parameter: IDENTIFIER "=" (IDENTIFIER | NUMBER)
IDENTIFIER: /[_a-zA-Z][_a-zA-z0-9]*/
NUMBER: /[+-]?(0[xdob])?[0-9\.]+/
ANY: /.+/
%import common.WS
%ignore WS
COMMENTS: /#.*/
%ignore COMMENTS
```

### Concepts

- **epoch** region is a region that contains one or more constraints.
- **constraint** is single line statement that specifies the constraint type and its content. Each constraint is expressed by a relationship among operations, events, and anchors.
- **operation** is the ID of the operation in the proto-assembly file. Each operation, depends on its internal instruction list, can be interpreted as a sequence of events and their transformation. The event name in resource operation is used to represent the specific event for operations using ``FSM`` instructions to construct multi-state FSM. For control operations, each instruction will be a new event. The event name always starts with `e` and followed by a number. They are always numerically ordered. For example, the first, second and third event of operation `op0` will be `op0.e0`, `op0.e1` and `op0.e2` respectively.
- **anchor** is the specific instances of events in different loop iterations implemented by the `rep` instruction. The anchor name is the event name followed by a sequence of loop iteration number. For example, the first event of operation `op0` in the second loop iteration will be `op0.e0[1]`. The left-to-right order of the loop iteration number is from the outermost loop to the innermost loop.

### Example

The following is an example of a constraint file that specifies the constraints for the proto-assembly program written in the previous section:

```cstr
epoch <rb0> {
    linear ( input_r == input_w )
    linear ( route0r < read_ab )
    linear ( route1wr < write_a )
    linear ( route1wr < write_b )
    linear ( read_ab.e0[0] == write_a.e0[0] - 1 )
    linear ( read_ab.e0[1] == write_b.e0[0] - 1 )
    linear ( write_a < read_a_seq )
    linear ( write_b < read_b_seq )
    linear ( swb < read_a_seq )
    linear ( read_a_seq == read_b_seq )
    linear ( read_a_seq + 1 > compute )
    linear ( write_c_seq == read_a_seq + 2 )

    linear ( compute != route1wr )
    linear ( compute != swb )

    linear ( read_c.e0[0] > write_c_seq.e0[15] )

    linear ( write_c == read_c + 1 )
    linear ( output_r > write_c )
    linear ( output_r == output_w )
}
```