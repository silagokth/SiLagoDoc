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

```void RLB(int MASK, int ADDR, int VWR_SEL);```

#### Description

`RLB` instructions transfer data from SRAM to VWR. The address of SRAM is defined by `ADDR`, and the id of VWR is defined by `VWR_SEL`. This instruction can transfer the data block partially. The `MASK` field is used to control which part of the data block should be copied. If `MASK[i]==1`, the *i*-th word of the data block will be copied.  

### WLB

#### Instruction Format

```WLB MASK ADDR VWR_SEL```

#### Primitive Function Format

```void WLB(int MASK, int ADDR, int VWR_SEL);```

#### Description

`WLB` instructions transfer data from VWR to SRAM. The address of SRAM is defined by `ADDR`, and the id of VWR is defined by `VWR_SEL`. This instruction can transfer the data block partially. The `MASK` field is used to control which part of the data block should be copied. If `MASK[i]==1`, the *i*-th word of the data block will be copied.  
