# Instruction Set

## Control Instructions

### NOP

```NOP```

`NOP` instruciton is used for delay based synchronization. It cannot be directly used in untimed C++ input program. Each `NOP` has a 1-cycle delay.

## Data Transfer Instructions

### RLB

```RLB MASK ADDR VWR_SEL```

`RLB` instructions transfer data from SRAM to VWR. The address of SRAM is defined by `ADDR`, and the id of VWR is defined by `VWR_SEL`. This instruction can transfer the data block partially. The `MASK` field is used to control which part of the data block should be copied. If `MASK[i]==1`, the *i*-th word of the data block will be copied.  

### WLB

```WLB MASK ADDR VWR_SEL```

`WLB` instructions transfer data from VWR to SRAM. The address of SRAM is defined by `ADDR`, and the id of VWR is defined by `VWR_SEL`. This instruction can transfer the data block partially. The `MASK` field is used to control which part of the data block should be copied. If `MASK[i]==1`, the *i*-th word of the data block will be copied.

### VMV

```VMV VWR_SEL ADDR```

`VMV` move the data from the VWR inside VFU **R_IN**. The `REG` has to be **R_IN**. The `SECTION` is the area that current VFU can write to. The `OFFSET` is the address inside the section.

## Data Rearrangement Instruction

### GLMV

```GLMV VWR_SEL ADDR EN0 BLOCK0 .... EN11 BLOCK11```

`GLMV` shuffles a VWR contents and store it back to itself. The shuffle pattern is given by pair `(EN0, BLOCK0)` to `(EN11, BLOCK11)`. When `ENi` is non-zero, the block with address `BLOCKi` will be copied to block `ADDR+i`.

### RMV

```RMV VWR ADDR```

`RMV` instruction write the **R_OUT** register to VWR. The `REG` has to be the **R_OUT**. The `SECTION` is the area that current VFU can write to. The `OFFSET` is the address inside the section.

### PACK

```PACK R1PACK R2PACK OUT_MUX_EN RRR VWR_SEL ADDR```

`PACK` instruction is primarily used for supporting soft-SIMD operations by means of guard bits. The filed `OUT_MUX_EN` is used to write the low-address half of output to the **R_OUT**. The filed `RRR` choosing whether the Reverse River Router is enabled and what is the output source. `R1PACK` and `R2PACK` are of the format: ```IN_LOC, SW_LEN, SW_OFFSET, OUT_LOC, SW_LEN_OUT, SW_COUNT```. Everything is aligned at 4 bits. Each part has the following meaning:

* IN_LOC : Starting location of the packing
* SW_LEN : Length of the subword in nibbles. Example: If subword length is 8, SW_LEN = 2
* SW_OFFSET : The number of leading unused bits.
* OUT_LOC : Starting location of the output (considering R1 | R2 )
* SW_LEN_OUT: Length of the output subword in nibbles. If SW_LEN_OUT > SW_LEN, then leading guard bits are introduced, else the subword is trimmed.
* SW_COUNT : The number of subwords considered.

### PERM

```PERM OUT_MUX_EN RRR VWR_SEL ADDR```

`PERM` instruction is used to permute subword at the level of channels. If `OUT_MUX_EN=1`, then (0:63) are written to **R_OUT**.  If `RRR=11`, the top output (64:127) of the shuffler is written to `VWR[VWR_SEL][ADDR]`. If `RRR=0x`, then **R_OUT** is written to the VWR. If `OUT_MUX_EN=0`, then (0:63) is lost. If `RRR=0x`, then (64:127) are lost.

In the primitive function format, the field `MODE` specifies the storage mode:

* MODE=0: Low address half section goes to V_OUT, high address half section goes to VWR.
* MODE=1: Low address half section goes to V_OUT, high address half section is lost.
* MODE=2: Low address half section is lost, high address half section goes to VWR.

The `STAGE` specify how many times it will rotate. If it's positive, it rotate to right hand side, otherwise, left hand side.

Register `V1` and `V2` must bind to the VFU internal register 1 and 2. `V_OUT` must bind to output register unless `MODE=2`, in such case, the `V_OUT` can be a dummy variable without binding information.

The `SECTION` and `OFFSET` together specifify the address in the VWR. The `SECTION` is the VWR section corresponding to the VFU and the `OFFSET` is the address inside the VWR section.

## Computaion Instruction

### VFUX

```VFUX VWR_SEL ADDR SRC_MUX INP_IN FU FU_MODE OUT_REG OUT_MUX_EN```

`VFUX` performs the computation based on field `FU`. It supports the following operations:

* Bias [BIAS] : X + C1
* BN [BN] : X*C2 + C1
* Partial sum [PSUM]: X + Y
* Avg Pool [AVGP] : ACC = X + Y
* [AVGPi] : ACC = X + ACC
* Max Pool [MAXP] : ACC = Max (X, Y) [MAXPi] : ACC = Max(X, ACC) Assumes that the output is made available every cycle, If the output is disabled, then it will not update the output registers (R_OUT, R1 and R2).
* RELU: If X > 0 , X else 0
* CLIP: If X < 0, out: 0, elif x <= T , out : x, else out: T
* SIG, TANH : Sigmoid and Tanh LUT based Sigmoid implementation
* SMX : (Softmax) Not supported yet.
* Multiply [MULT] : X*Y
* SHift [SHF] : X >> C1

For two-input FU operations, the first input to the FU is always **R_IN**. If the `SRC_MUX = 0`, the second input is `VWR[VWR_SEL][ADDR]`, else it is **R_OUT**.

For single input FU operations, `INP_IN` decides between **R_IN** and VWR as input. Setting `INP_IN=b10` indicates two operands are used. `INP_IN=b00` is used when single input FU operation with **R_IN** as input. and `b01` sets VWR as input.

`OUT_REG` decides where the output of the FU is written.

* 01 : to **R1**
* 10 : to **R2**
* 11 : to both **R1** and **R2**
* 00 : to **R_OUT**

`FU_MODE` is a 4 bit field to specify the precision of the operands and to specify which parts of the operands would be used for current computation. Let us consider precision P as the default precision.

* xx00 : P for IN1
* xx10 : 2P for IN1 and the lower bits of IN2 are used.
* xx11 : 2P for IN1 and the higher bits of IN2 are used.
* 00xx : P for IN2
* 10xx : 2P for IN2 and the lower bits of IN1 are used.
* 11xx : 2P for IN2 and the higher bits of IN1 are used.

!!! Example
    `FU_MODE=0010` would imply that **R_IN** has 32x16 inputs and the output of `SRC_MUX [0:31]x8` are used. In the next step, **R_IN** should be loaded and then `FU_MODE=0011`.

    In case `FU_MODE=1xxx` or `FU_MODE=xx1x`, only half of the internal compute blocks would be used and these outputs would be written to appropriate register.

### CAL

```CAL OP R0 R1 R2 RI1 RI2```

`CAL` performs the scalar operation on registers or immediate integer values: `R0 <- OP(R1, R2)`

The `OP` defines the supported operations:

* `0000`: ADD
* `0001`: SUB
* `0010`: MUL
* `0011`: DIV
* `0100`: MOD
* `0101`: Logic AND
* `0110`: Logic OR
* `0111`: Logic NOT
* `1000`: Bit AND
* `1001`: Bit OR
* `1010`: Bit NOT
* `1011`: Bit XOR

The `R0` is the return register address. `R1` is the first operand registers if `RI1` is `0`, otherwise, the `R1` contains a immediate value. The same rule applies to `R2` and `RI2`.

## Control Instructions

### BRN

```BRN MODE SRC NPC```

`BRN` instruction modifies the PC counter to implement loops and branches.

The brancher recieves 4-bit flag:

* Z (Zero) flag: This flag is set when the result of an instruction has a zero value or when a comparison of two data returns an equal result.
* N (Negative) flag: This flag is set when the result of an instruction has a negative value.
* C (Carry) flag: This flag is for unsigned data processing—for example, in add (ADD) it is set when an overflow occurs; in subtract (SUB) it is set when a borrow did not occur.
* V (Overflow) flag: This flag is for signed data processing; for example, in an add (ADD), when two positive values added together produce a negative value, or when two negative values added together produce a positive value.

The `MODE` defines how to inteprete the above flag. Through the `MODE` field, it can implement the following condition:

* `000`: Uncondition
* `001`: Great (signed)
* `010`: Great and Equal (signed)
* `011`: Less (signed)
* `100`: Less and Equal (signed)
* `101`: Equal / Is Zero
* `110`: Not Equal / Is not Zero

The `SRC` defines where should be the source that generate the flag. It can be:

* `0`: Scalar ALU
* `1`: VFU0

The `NPC` is the next PC if the branch happens. It's a relative address.