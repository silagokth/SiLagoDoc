# Primitive Function

## Data Transfer

### RLB

```void RLB(SramVector sv, VwrVector& vv, int addr, int mask);```

`RLB` function transfers data from SRAM vector `sv` to a VWR vector `vv`. The address of SRAM is defined by `addr`. This instruction can transfer the data block partially. The `mask` field is used to control which part of the data block should be copied. If `mask[i]==1`, the *i*-th word of the data block will be copied.  

### WLB

```void WLB(SramVector& sv, VwrVector vv, int addr, int mask);```

`WLB` function works just like `RLB`. The only difference is that the data transfer direction is from VWR to SRAM.

### VMV

```void VMV(int slot, VwrVector vv, RegVector& rv, int addr);```

`VMV` function transfers data from VWR vector `vv` to an VFU internal register `rv`. The `slot` defines which VFU will carry out this data transfer. It also restrict the data section on the VWR that can be seen by the VFU. The data block that is copied is defined by `addr` with respect to the section start address.  

### RMV

```void RMV(int slot, VwrVector& vv, RegVector rv, int addr);```

`RMV` function works just like `VMV`. The only difference is that the data transfer direction is from Register to VWR.

## Data Rearrangement

### GLMV

```void GLMV(VwrVector& vv, int addr, int en0, int block0, ..., int en11, int block11);```

`GLMV` shuffles a VWR contents and store it back to itself. The shuffle pattern is given by pair `(en0, block0)` to `(en11, block11)`. When `eni` is non-zero, the block with address `blocki` will be copied to block `addr+i`.

### PACK

!!! warning
    Not complete

```void PACK(int R1PACK, int R2PACK, int OUT_MUX_EN, int RRR, VwrVector VWR, int ADDR);```

`PACK` instruction is primarily used for supporting soft-SIMD operations by means of guard bits. The filed `OUT_MUX_EN` is used to write the low-address half of output to the **R_OUT**. The filed `RRR` choosing whether the Reverse River Router is enabled and what is the output source. `R1PACK` and `R2PACK` are of the format: ```IN_LOC, SW_LEN, SW_OFFSET, OUT_LOC, SW_LEN_OUT, SW_COUNT```. Everything is aligned at 4 bits. Each part has the following meaning:

* IN_LOC : Starting location of the packing
* SW_LEN : Length of the subword in nibbles. Example: If subword length is 8, SW_LEN = 2
* SW_OFFSET : The number of leading unused bits.
* OUT_LOC : Starting location of the output (considering R1 | R2 )
* SW_LEN_OUT: Length of the output subword in nibbles. If SW_LEN_OUT > SW_LEN, then leading guard bits are introduced, else the subword is trimmed.
* SW_COUNT : The number of subwords considered.

### PERM

```void PERM_R(int slot, int stage, RegVector rv1, RegVector rv2, RegVector& rv_out);```

```void PERM_V(int slot, int stage, RegVector rv1, RegVector rv2, VwrVector& vv_out, int addr);```

```void PERM_RV(int slot, int stage, RegVector rv1, RegVector rv2, RegVector& rv_out, VwrVector& vv_out, int addr);```

`PERM` function has 3 forms depending on where the output goes. It is used to permute subword at the level of channels.

* `PERM_RV`: Low address half section goes to rv_out, high address half section goes to vv_out.
* `PERM_R`: Low address half section goes to rv_out, high address half section is lost.
* `PERM_V`: Low address half section is lost, high address half section goes to vv_out.

The `stage` specify how many times it will rotate. If it's positive, it rotate to right hand side, otherwise, left hand side. Register `rv1` and `rv2` must bind to the VFU internal register 1 and 2. `rv_out` must bind to output register in VFU. The `addr` specifify the address in the VWR.

## Computaion

### VFUX

```void VFUX_RV_R (int slot, int mode, int func, RegVector rv_in1, VwrVector vv_in2, int addr, RegVector& rv_out1);```

```void VFUX_RV_RR(int slot, int mode, int func, RegVector rv_in1, VwrVector vv_in2, int addr, RegVector& rv_out1, RegVector& rv_out2);```

```void VFUX_RR_R (int slot, int mode, int func, RegVector rv_in1, RegVector rv_in2, RegVector& rv_out1);```

```void VFUX_RR_RR(int slot, int mode, int func, RegVector rv_in1, RegVector rv_in2, RegVector& rv_out1, RegVector& rv_out2);```

```void VFUX_V_R  (int slot, int mode, int func, VwrVector vv_in1, int addr, RegVector& rv_out1);```

```void VFUX_V_RR (int slot, int mode, int func, VwrVector vv_in1, int addr, RegVector& rv_out1, RegVector& rv_out2);```

```void VFUX_R_R  (int slot, int mode, int func, RegVector rv_in1, RegVector& rv_out1);```

```void VFUX_R_RR (int slot, int mode, int func, RegVector rv_in1, RegVector& rv_out1, RegVector& rv_out2);```

`VFUX` function has 8 different forms. They have different inputs and/or outputs. The naming convention pattern is `VFUX_<INPUT>_<OUTPUT>`. For example, `VFUX_RV_R` means the function is a two-input function. The first input comes from a register and the second input comes from the VWR. The output will be stored in just 1 register. `VFUX_R_RR` means that the function has only 1 input, and it's from a register. The output will be very wide which needs two regiters to store it.

`mode` is a 4 bit field to specify the precision of the operands and to specify which parts of the operands would be used for current computation. Let us consider precision P as the default precision.

* xx00 : P for IN1
* xx10 : 2P for IN1 and the lower bits of IN2 are used.
* xx11 : 2P for IN1 and the higher bits of IN2 are used.
* 00xx : P for IN2
* 10xx : 2P for IN2 and the lower bits of IN1 are used.
* 11xx : 2P for IN2 and the higher bits of IN1 are used.

`func` describes the supported function:

* 1: Bias [BIAS] : X + C1
* 2: BN [BN] : X*C2 + C1
* 3: Partial sum [PSUM]: X + Y
* 4: Avg Pool [AVGP] : ACC = X + Y
* 5: [AVGPi] : ACC = X + ACC
* 6: Max Pool [MAXP] : ACC = Max (X, Y) [MAXPi] : ACC = Max(X, ACC) Assumes that the output is made available every cycle, If the output is disabled, then it will not update the output registers (R_OUT, R1 and R2).
* 7: RELU: If X > 0 , X else 0
* 8: CLIP: If X < 0, out: 0, elif x <= T , out : x, else out: T
* 9: SIG, TANH : Sigmoid and Tanh LUT based Sigmoid implementation
* 10: SMX : (Softmax) Not supported yet.
* 11: Multiply [MULT] : X*Y
* 12: SHift [SHF] : X >> C1

`rv_in1` should always bind to the input register in VFU. If there are two output register, `rv_out1` is always bind to register 1, and `rv_out2` is always bind to register 2. If there is only 1 output register, it can be register 1, 2 or output register.