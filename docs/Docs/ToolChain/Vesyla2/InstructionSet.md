# Instruction Set

## Control Instructions

### NOP

#### Instruction Format

```NOP CYCLES```

#### Primitive Function Format

```N/A```

#### Description

`NOP` instruciton is used for delay based synchronization. It cannot be directly used in untimed C++ input program. The `CYCLES` field specifies how many cycles it will dealy.

## Data Transfer Instructions

### RLB

#### Instruction Format

```RLB MASK ADDR VWR_SEL```

#### Primitive Function Format

```void RLB(SramVector SRAM, VwrVector VWR, int ADDR, int MASK);```

#### Description

`RLB` instructions transfer data from SRAM to VWR. The address of SRAM is defined by `ADDR`, and the id of VWR is defined by `VWR_SEL`. This instruction can transfer the data block partially. The `MASK` field is used to control which part of the data block should be copied. If `MASK[i]==1`, the *i*-th word of the data block will be copied.  

### WLB

#### Instruction Format

```WLB MASK ADDR VWR_SEL```

#### Primitive Function Format

```void WLB(SramVector SRAM, VwrVector VWR, int ADDR, int MASK);```

#### Description

`WLB` instructions transfer data from VWR to SRAM. The address of SRAM is defined by `ADDR`, and the id of VWR is defined by `VWR_SEL`. This instruction can transfer the data block partially. The `MASK` field is used to control which part of the data block should be copied. If `MASK[i]==1`, the *i*-th word of the data block will be copied.

### VMV

#### Instruction Format

```VMV VWR_SEL ADDR```

#### Primitive Function Format

```void VMV(VwrVector VWR, RegVector REG, int ADDR);```

#### Description

`VMV` shuffles move the data from the VWR inside VFU **R_IN**.

## Data Rearrangement Instruction

### GLMV

#### Instruction Format

```GLMV VWR_SEL ADDR EN0 BLOCK0 .... EN11 BLOCK11```

#### Primitive Function Format

```void GLMV(VwrVector VWR, int ADDR, int EN0, int BLOCK0, ..., int EN11, int BLOCK11);```

#### Description

`GLMV` shuffles a VWR contents and store it back to itself. The shuffle pattern is given by pair `(EN0, BLOCK0)` to `(EN11, BLOCK11)`. When `ENi` is non-zero, the block with address `BLOCKi` will be copied to block `ADDR+i`.

### RMV

#### Instruction Format

```RMV VWR_SEL ADDR```

#### Primitive Function Format

```void RMV(int VWR_SEL, int ADDR);```

#### Description

`RMV` instruction write the **R_OUT** register to VWR.

### PACK

#### Instruction Format

```PACK R1PACK R2PACK OUT_MUX_EN RRR VWR_SEL ADDR```

#### Primitive Function Format

```void PACK(int R1PACK, int R2PACK, int OUT_MUX_EN, int RRR, VwrVector VWR, int ADDR);```

#### Description

`PACK` instruction is primarily used for supporting soft-SIMD operations by means of guard bits. The filed `OUT_MUX_EN` is used to write the low-address half of output to the **R_OUT**. The filed `RRR` choosing whether the Reverse River Router is enabled and what is the output source. `R1PACK` and `R2PACK` are of the format: ```IN_LOC, SW_LEN, SW_OFFSET, OUT_LOC, SW_LEN_OUT, SW_COUNT```. Everything is aligned at 4 bits. Each part has the following meaning:

* IN_LOC : Starting location of the packing
* SW_LEN : Length of the subword in nibbles. Example: If subword length is 8, SW_LEN = 2
* SW_OFFSET : The number of leading unused bits.
* OUT_LOC : Starting location of the output (considering R1 | R2 )
* SW_LEN_OUT: Length of the output subword in nibbles. If SW_LEN_OUT > SW_LEN, then leading guard bits are introduced, else the subword is trimmed.
* SW_COUNT : The number of subwords considered.

### PERM

#### Instruction Format

```PERM OUT_MUX_EN RRR VWR_SEL ADDR```

#### Primitive Function Format

```void PERM(int OUR_MUX_EN, int RRR, VwrVector VWR, int ADDR);```

#### Description

`PERM` instruction is used to permute subword at the level of channels. If `OUT_MUX_EN=1`, then (0:63) are written to **R_OUT**.  If `RRR=11`, the top output (64:127) of the shuffler is written to `VWR[VWR_SEL][ADDR]`. If `RRR=0x`, then **R_OUT** is written to the VWR.

!!! WARNING
    If `OUT_MUX_EN=0`, then (0:63) is lost. If `RRR=0x`, then (64:127) are lost.

## Computaion Instruction

### VFUX

#### Instruction Format

```VFUX VWR_SEL ADDR SRC_MUX INP_IN FU FU_MODE OUT_REG OUT_MUX_EN```

#### Primitive Function Format

```void VFUX(RegVector IN1, VwrVector IN2, int ADDR, int FU, int FU_MODE, RegVector OUT);```

```void VFUX(RegVector IN1, RegVector IN2, int FU, int FU_MODE, RegVector OUT);```

```void VFUX(VwrVector IN1, int ADDR, int FU, int FU_MODE, RegVector OUT);```

```void VFUX(RegVector IN1, int FU, int FU_MODE, RegVector OUT);```

#### Description

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