# Instruction Set

## Instructions

### 0000 HALT

```text
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0
```

Field                | Position | Width | Range/Value | Description
---------------------|----------|-------|-------------|-------------------------
instr_code           | [26, 23] | 4     | b'0000      | HALT instruction code
unused               | [22, 0]  | 23    | 0           | Unused

### 0001 - REFI

```text
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  0  0  1  A  A  B  B  C  D  D  D  D  D  D  E  F  F  F  F  F  F  G  H  H  H  H
0  0  1  0  I  J  J  J  J  J  J  K  L  M  M  M  M  M  N  N  N  N  N  O  O  O  O
0  0  1  1  P  Q  Q  Q  Q  Q  Q  0  0  0  0  0  0  R  R  S  T  T  0  0  0  U  V
```

Field                | Position | Width | Range/Value | Description
---------------------|----------|-------|-------------|-------------------------
instr_code           | [26, 23] | 4     | b'0001      | REFI1 instruction code
port_no              | [22, 21] | 2     | [0, 3]      | Selects one of the RFile ports
extra                | [20, 19] | 2     | [0, 3]      | How many following instructions.
init_addr_sd         | 18       | 1     | [0, 1]      | [0] init_addr is static; [1] init_addr is from RACCU.
init_addr            | [17, 12] | 6     | [0, 63]     | Static init address or RACCU register.
l1_iter_sd           | 11       | 1     | [0, 1]      | [0] Level 1 iteration is static; [1] L1 iteration is from RACCU.
l1_iter              | [10, 5]  | 6     | [0, 63]     | Static L1 iteration or RACCU register.
init_delay_sd        | 4        | 1     | [0, 1]      | [0] init_delay is static; [1] init_delay is from RACCU.
init_delay           | [3, 0]   | 4     | [0, 15]     | Static init delay or RACCU register.
unused               | [26, 23] | 4     | b'0010      | Deprecated
l1_step_sd           | [22]     | 1     | [0, 1]      | [0] l1_step is static; [1] l1_step is from RACCU
l1_step              | [21, 16] | 6     | [0, 63]     | Static level 1 step value or RACCU register
l1_step_sign         | [15]     | 1     | [0, 1]      | Sign bit of l1_step
l1_delay_sd          | [14]     | 1     | [0, 1]      | [0] l1_delay is static; [1] l1_delay is from RACCU
l1_delay             | [13, 10] | 4     | [0, 15]     | Static level 1 delay or RACCU register
l2_iter_sd           | [9]      | 1     | [0, 1]      | [0] l2_iter is static; [1] l2_iter is from RACCU
l2_iter              | [8, 4]   | 5     | [0, 31]     | Static level 2 iteration or RACCU register
l2_step              | [3, 0]   | 4     | [0, 15]     | Level 2 step value
unused               | [26, 23] | 4     | b'0011      | Deprecated
l2_delay_sd          | [22]     | 1     | [0, 1]      | [0] l2_delay is static; [1] l2_delay is from RACCU.
l2_delay             | [21, 16] | 6     | [0, 63]     | Static level 2 delay or RACCU register.
unused               | [15, 10] | 6     | 0           | Deprecated
l1_delay_ext         | [9, 8]   | 2     | [0, 3]      | Extended bits for l1_delay
l2_iter_ext          | [7]      | 1     | [0, 1]      | Extended bits for l2_iter
l2_step_ext          | [6, 5]   | 2     | [0, 3]      | Extended bits for l2_step
unused               | [4, 2]   | 3     | 0           | Deprecated
dimarch              | [1]      | 1     | [0, 1]      | [0] Not DiMArch; [1] DiMArch mode
compress             | [0]      | 1     | [0, 1]      | [0] Not compressed; [1] Compressed

### 0100 - DPU

```text
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  1  0  0  A  A  A  A  A  B  B  C  C  D  D  E  F  G  G  G  G  G  G  G  G  H  H
```

Field                | Position | Width | Range/Value | Description
---------------------|----------|-------|-------------|-------------------------
instr_code           | [26, 23] | 4     | b'0100      | DPU instruction code
mode                 | [22, 18] | 5     | [0, 12]     | Configures the valid dpu mode
control              | [17, 16] | 2     | [0, 3]      | [00] no saturation, integer; [01] no saturation, fixed-point; [10] saturation, integer; [11] saturation, fixed-point.
not_used             | [15, 10] | 6     | b'000010    | Deprecated
acc_clear            | [9, 2]   | 8     | [0, 255]    | The DPU accumulator register clear threshold.
io_change            | [1, 0]   | 2     | [0, 3]      | [00] no change; [01]negate in1; [10] negate in2; [11] return abosolute value of output.

### 0101 SWB

```text
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  1  0  1  1  A  B  C  D  D  D  E  F  F  F  0  0  0  0  0  0  0  0  0  0  0  0
```

Field                | Position | Width | Range/Value | Description
---------------------|----------|-------|-------------|-------------------------
instr_code           | [26, 23] | 4     | b'0110      | SWB instruction code
active               | [22]     | 1     | 1           | Deprecated, always 1
src_row              | [21]     | 1     | [0, 1]      | The source DRRA row
src_block            | [20]     | 1     | [0, 1]      | [0] RF; [1] DPU
src_port             | [19]     | 1     | [0, 1]      | Source port number
hb_index             | [18, 16] | 3     | [0, 6]      | Index of horizontal bus
send_to_other_row    | [15]     | 1     | [0, 1]      | Flag of whether src and dest row are equal
v_index              | [14, 12] | 3     | [0, 5]      | Index of vertical bus
unused               | [11, 0]  | 12    | 0           | Deprecated

### 0110 JUMP

```text
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  1  1  0  A  A  A  A  A  A  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0
```

Field                | Position | Width | Range/Value | Description
---------------------|----------|-------|-------------|-------------------------
instr_code           | [26, 23] | 4     | b'0110      | JUMP instruction code
pc                   | [22, 17] | 6     | [0, 63]     | The target address
unused               | [16, 0]  | 17    | 0           | Deprecated

### 0111 WAIT

```text
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  1  1  1  A  B  B  B  B  B  B  B  B  B  B  B  B  B  B  B  0  0  0  0  0  0  0
```

Field                | Position | Width | Range/Value | Description
---------------------|----------|-------|-------------|-------------------------
instr_code           | [26, 23] | 4     | b'0111      | DELAY instruction code
cycle_sd             | [22]     | 1     | [0, 1]      | [0] cycle is static; [1] cycle is from RACCU
cycle                | [21, 7]  | 15    | [0, 32767]  | Static cycle or RACCU register
unused               | [6, 0]   | 7     | 0           | 0

### 1000 LOOP

```text
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
1  0  0  0  A  B  B  C  C  C  C  C  C  D  E  E  E  E  E  E  F  G  G  G  G  G  G
H  I  I  I  I  I  I  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0
```

Field                | Position | Width | Range/Value | Description
---------------------|----------|-------|-------------|-------------------------
instr_code           | [53, 50] | 4     | b'1000      | LOOPH instruction code
extend               | [49]     | 1     | [0, 15]     | 0:No extension; 1:Extended
loopid               | [48, 47] | 2     | [0, 3]      | The id of nested loops
endpc                | [46, 41] | 6     | [0, 63]     | The PC where loop ends
start_sd             | [40]     | 1     | [0, 1]      | 0:start is static; 1: start is from RACCU
start                | [39, 34] | 6     | [-32, 31]   | Start address, either static or from RACCU
iter_sd              | [33]     | 1     | [0, 1]      | 0:iter is static; 1: iter is from RACCU
iter                 | [32, 27] | 6     | [0, 63]     | Iteration, either static or from RACCU
step_sd              | [26]     | 1     | [0, 1] / 0  | 0:step is static; 1: step is from RACCU
step                 | [25, 20] | 6     | [0, 63] / 1 | Step, either static or from RACCU
unused               | [19, 0]  | 20    | 0           | Unused

### 1010 RACCU

```text
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  1  1  1  A  A  A  B  C  C  C  C  C  C  C  D  E  E  E  E  E  E  E  F  F  F  F
```

Field                | Position | Width | Range/Value | Description
---------------------|----------|-------|-------------|-------------------------
instr_code           | [26, 23] | 4     | b'1010      | RACCU instruction code
mode                 | [22, 20] | 3     | [0, 7]      | Operation mode
operand1_sd          | [19, 19] | 1     | [0, 1]      | [0] operand1 is static; [1] operand1 is from RACCU register
operand1             | [18, 12] | 7     | [-64, 63]   | Operand 1
operand2_sd          | [11, 11] | 1     | [0, 1]      | [0] operand2 is static; [1] operand2 is from RACCU register
operand2             | [10, 4]  | 7     | [-64, 63]   | Operand 2
result               | [4, 0]   | 4     | [0, 15]     | Result register address

### 1011 BRANCH

```text
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  1  1  1  A  A  B  B  B  B  B  B  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0
```

Field                | Position | Width | Range/Value | Description
---------------------|----------|-------|-------------|-------------------------
instr_code           | [26, 23] | 4     | b'1011      | BRANCH instruction code
mode                 | [22, 21] | 2     | [0, 4]      | The conditional branch mode
false_pc             | [20, 15] | 6     | [0, 63]     | Configures the false address
unused               | [14, 0]  | 15    | 0           | Deprecated

### 1100 ROUTE

```text
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  1  1  1  A  B  B  B  C  D  D  D  0  0  0  0  0  0  E  0  0  0  0  0  0  0  0
```

Field                | Position | Width | Range/Value | Description
---------------------|----------|-------|-------------|-------------------------
instr_code           | [26, 23] | 4     | b'1100      | ROUTE instruction code
src_row              | [22]     | 1     | [0, 1]      | Source DiMArch row
src_col              | [21, 19] | 3     | [0, 7]      | Source DiMArch column
dest_row             | [18]     | 1     | [0, 1]      | Destination DiMArch row
dest_col             | [17, 15] | 3     | [0, 7]      | Destination DiMArch column
unused               | [14, 9]  | 6     | 0           | Deprecated
select_drra_row      | [8]      | 1     | [0, 1]      | [0] source is origin; [1] destination is origin
unused               | [7, 0]   | 8     | 0           | Deprecated

### 1101 SRAM

```text
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
1  1  0  1  A  B  B  B  B  B  B  B  C  C  C  C  D  D  D  D  D  D  D  E  E  E  E
E  E  E  E  F  F  F  F  F  F  G  G  G  G  G  G  G  H  H  H  H  H  H  H  H  I  I
I  I  I  I  J  K  L  M  N  O  P  Q  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0
```

Field                | Position | Width         | Range/Value | Description
---------------------|----------|---------------|-------------|-------------------------
instr_code           | [80, 77] | 4             | b'1101      | SRAMR instruction code
rw                   | [76]     | 1             | [0, 1]      | Read or Write
init_addr            | [75, 69] | 7<sup>*</sup> | [0, 127]    | Initial address
init_delay           | [68, 65] | 4             | [0, 15]     | Initial delay
l1_iter              | [64, 58] | 7<sup>*</sup> | [0, 127]    | Level 1 iteration
l1_step              | [57, 50] | 8<sup>*</sup> | [-128, 127] | Level 1 step
l1_delay             | [49, 44] | 6             | [0, 63]     | Level 1 delay
l2_iter              | [43, 37] | 7<sup>*</sup> | [0, 127]    | Level 2 iteration
l2_step              | [36, 29] | 8<sup>*</sup> | [-128, 127] | Level 2 step
l2_delay             | [28, 23] | 6             | [0, 63]     | Level 2 delay
init_addr_sd         | [22]     | 1             | [0, 1]      | Static or from RACCU
l1_iter_sd           | [21]     | 1             | [0, 1]      | Static or from RACCU
l2_iter_sd           | [20]     | 1             | [0, 1]      | Static or from RACCU
init_delay_sd        | [19]     | 1             | [0, 1]      | Static or from RACCU
l1_delay_sd          | [18]     | 1             | [0, 1]      | Static or from RACCU
l2_delay_sd          | [17]     | 1             | [0, 1]      | Static or from RACCU
l1_step_sd           | [16]     | 1             | [0, 1]      | Static or from RACCU
l2_step_sd           | [15]     | 1             | [0, 1]      | Static or from RACCU
unused               | [14, 0]  | 15            | 0           | Unused

<sup>*</sup> The number of bits of the marked fields depends on the size of the SRAM. The current number are set according to a 128 row SRAM, each row is of 256 bits.
