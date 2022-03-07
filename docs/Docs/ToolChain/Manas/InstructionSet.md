# Instruction Set

## Instructions

--8<-- "Docs/ToolChain/Manas/InstructionSet/isa.md"

<!--
!!! Note
    The fields marked by **bold text** in each instruction indicate that they are controllable. These fields can be directly set via assembly code.

### 0000 HALT

```
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
1  1  1  1  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0
```

Field                    | Position | Width | Range/Value | Description
-------------------------|----------|-------|-------------|-------------------------
instr_code               | [26, 23] | 4     | b'0000      | HALT instruction code
unused                   | [22, 0]  | 23    | 0           | Unused

### 0001 - REFI

```
80 79 78 77 76 75 74 73 72 71 70 69 68 67 66 65 64 63 62 61 60 59 58 57 56 55 54
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  0  0  1  A  A  B  B  C  D  D  D  D  D  D  E  F  F  F  F  F  F  G  H  H  H  H
53 52 51 50 49 48 47 46 45 44 43 42 41 40 39 38 37 36 35 34 33 32 31 30 29 28 27
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  0  1  0  I  J  J  J  J  J  J  K  L  M  M  M  M  M  M  M  M  M  M  N  N  N  N
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  0  1  1  O  P  P  P  P  P  P  0  0  0  0  0  0  Q  Q  R  S  S  0  0  0  T  U
```

Field                    | Position | Width | Range/Value | Description
-------------------------|----------|-------|-------------|-------------------------
instr_code               | [80, 77] | 4     | b'0001      | REFI1 instruction code
**port_no**              | [76, 75] | 2     | [0, 3]      | Selects one of the RFile ports
**extra**                | [74, 73] | 2     | [0, 3]      | How many following instructions.
**init_addr_sd**         | [72]     | 1     | [0, 1]      | [0] init_addr is static; [1] init_addr is from RACCU.
**init_addr**            | [71, 66] | 6     | [0, 63]     | Static init address or RACCU register.
**l1_iter**              | [65, 60] | 6     | [0, 63]     | Static L1 iteration or RACCU register.
init_delay               | [59, 54] | 6     | [0, 63]     | Static init delay or RACCU register.
**l1_iter_sd**           | [53]     | 1     | [0, 1]      | [0] Level 1 iteration is static; [1] L1 iteration is from RACCU.
init_delay_sd            | [52]     | 1     | [0, 1]      | [0] init_delay is static; [1] init_delay is from RACCU.
unused                   | [51, 50] | 2     | b'10        | Deprecated
**l1_step_sd**           | [49]     | 1     | [0, 1]      | [0] l1_step is static; [1] l1_step is from RACCU
**l1_step**              | [48, 43] | 6     | [0, 63]     | Static level 1 step value or RACCU register
**l1_step_sign**         | [42]     | 1     | [0, 1]      | Sign bit of l1_step
l1_delay_sd              | [41]     | 1     | [0, 1]      | [0] l1_delay is static; [1] l1_delay is from RACCU
l1_delay                 | [40, 37] | 4     | [0, 15]     | Static level 1 delay or RACCU register
**l2_iter_sd**           | [36]     | 1     | [0, 1]      | [0] l2_iter is static; [1] l2_iter is from RACCU
**l2_iter**              | [35, 31] | 5     | [0, 31]     | Static level 2 iteration or RACCU register
**l2_step**              | [30, 27] | 4     | [0, 15]     | Level 2 step value
unused                   | [26, 23] | 4     | b'0011      | Deprecated
l2_delay_sd              | [22]     | 1     | [0, 1]      | [0] l2_delay is static; [1] l2_delay is from RACCU.
l2_delay                 | [21, 16] | 6     | [0, 63]     | Static level 2 delay or RACCU register.
unused                   | [15, 10] | 6     | 0           | Deprecated
l1_delay_ext             | [9, 8]   | 2     | [0, 3]      | Extended bits for l1_delay
**l2_iter_ext**          | [7]      | 1     | [0, 1]      | Extended bits for l2_iter
**l2_step_ext**          | [6, 5]   | 2     | [0, 3]      | Extended bits for l2_step
unused                   | [4, 2]   | 3     | 0           | Deprecated
**dimarch**              | [1]      | 1     | [0, 1]      | [0] Not DiMArch; [1] DiMArch mode
**compress**             | [0]      | 1     | [0, 1]      | [0] Not compressed; [1] Compressed

### 0100 - DPU

```
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  1  0  0  A  A  A  A  A  B  B  C  C  D  D  E  F  G  G  G  G  G  G  G  G  H  H
```

Field                    | Position | Width | Range/Value | Description
-------------------------|----------|-------|-------------|-------------------------
instr_code               | [26, 23] | 4     | b'0100      | DPU instruction code
**mode**                 | [22, 18] | 5     | [0, 12]     | Configures the valid dpu mode
**control**              | [17, 16] | 2     | [0, 3]      | [00] no saturation, integer; [01] no saturation, fixed-point; [10] saturation, integer; [11] saturation, fixed-point.
not_used                 | [15, 10] | 6     | b'000010    | Deprecated
**acc_clear**            | [9, 2]   | 8     | [0, 255]    | The DPU accumulator register clear threshold.
**io_change**            | [1, 0]   | 2     | [0, 3]      | [00] no change; [01]negate in1; [10] negate in2; [11] return abosolute value of output.

### 0101 SWB

```
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  1  0  1  1  A  B  C  D  D  D  E  F  F  F  0  0  0  0  0  0  0  0  0  0  0  0
```

Field                    | Position | Width | Range/Value | Description
-------------------------|----------|-------|-------------|-------------------------
instr_code               | [26, 23] | 4     | b'0110      | SWB instruction code
active                   | [22]     | 1     | 1           | Deprecated, always 1
**src_row**              | [21]     | 1     | [0, 1]      | The source DRRA row
**src_block**            | [20]     | 1     | [0, 1]      | [0] RF; [1] DPU
**src_port**             | [19]     | 1     | [0, 1]      | Source port number
**hb_index**             | [18, 16] | 3     | [0, 6]      | Index of horizontal bus. This is the column difference of the src and dest cell shifting by 2. For example if the path is from [0,0] to [1,2], the column difference is -2, so the hb_index = -2+2=0
**send_to_other_row**    | [15]     | 1     | [0, 1]      | Flag of whether src and dest row are equal
**v_index**              | [14, 12] | 3     | [0, 5]      | Index of vertical bus. This is the dest port. If destination is RF, the v_index is the port number, if the dest is DPU, the v_index is port number + 2.
unused                   | [11, 0]  | 12    | 0           | Deprecated

### 0110 JUMP

```
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  1  1  0  A  A  A  A  A  A  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0
```

Field                    | Position | Width | Range/Value | Description
-------------------------|----------|-------|-------------|-------------------------
instr_code               | [26, 23] | 4     | b'0110      | JUMP instruction code
**pc**                   | [22, 17] | 6     | [0, 63]     | The target address
unused                   | [16, 0]  | 17    | 0           | Deprecated

### 0111 WAIT

```
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  1  1  1  A  B  B  B  B  B  B  B  B  B  B  B  B  B  B  B  0  0  0  0  0  0  0
```

Field                    | Position | Width | Range/Value | Description
-------------------------|----------|-------|-------------|-------------------------
instr_code               | [26, 23] | 4     | b'0111      | DELAY instruction code
**cycle_sd**             | [22]     | 1     | [0, 1]      | [0] cycle is static; [1] cycle is from RACCU
**cycle**                | [21, 7]  | 15    | [0, 32767]  | Static cycle or RACCU register
unused                   | [6, 0]   | 7     | 0           | 0

### 1000 LOOP

```
53 52 51 50 49 48 47 46 45 44 43 42 41 40 39 38 37 36 35 34 33 32 31 30 29 28 27
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
1  0  0  0  A  B  B  C  C  C  C  C  C  D  E  E  E  E  E  E  F  G  G  G  G  G  G
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
H  I  I  I  I  I  I  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0
```

Field                    | Position | Width | Range/Value | Description
-------------------------|----------|-------|-------------|-------------------------
instr_code               | [53, 50] | 4     | b'1000      | LOOPH instruction code
**extend**               | [49]     | 1     | [0, 15]     | 0:No extension; 1:Extended
**loopid**               | [48, 47] | 2     | [0, 3]      | The id of nested loops
**endpc**                | [46, 41] | 6     | [0, 63]     | The PC where loop ends
**start_sd**             | [40]     | 1     | [0, 1]      | 0:start is static; 1: start is from RACCU
**start**                | [39, 34] | 6     | [-32, 31]   | Start address, either static or from RACCU
**iter_sd**              | [33]     | 1     | [0, 1]      | 0:iter is static; 1: iter is from RACCU
**iter**                 | [32, 27] | 6     | [0, 63]     | Iteration, either static or from RACCU
**step_sd**              | [26]     | 1     | [0, 1] / 0  | 0:step is static; 1: step is from RACCU
**step**                 | [25, 20] | 6     | [0, 63] / 1 | Step, either static or from RACCU
unused                   | [19, 0]  | 20    | 0           | Unused

### 1010 RACCU

```
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  1  1  1  A  A  A  B  C  C  C  C  C  C  C  D  E  E  E  E  E  E  E  F  F  F  F
```

Field                    | Position | Width | Range/Value | Description
-------------------------|----------|-------|-------------|-------------------------
instr_code               | [26, 23] | 4     | b'1010      | RACCU instruction code
**mode**                 | [22, 20] | 3     | [0, 7]      | Operation mode
**operand1_sd**          | [19, 19] | 1     | [0, 1]      | [0] operand1 is static; [1] operand1 is from RACCU register
**operand1**             | [18, 12] | 7     | [-64, 63]   | Operand 1
**operand2_sd**          | [11, 11] | 1     | [0, 1]      | [0] operand2 is static; [1] operand2 is from RACCU register
**operand2**             | [10, 4]  | 7     | [-64, 63]   | Operand 2
**result**               | [4, 0]   | 4     | [0, 15]     | Result register address

### 1011 BRANCH

```
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  1  1  1  A  A  B  B  B  B  B  B  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0
```

Field                    | Position | Width | Range/Value | Description
-------------------------|----------|-------|-------------|-------------------------
instr_code               | [26, 23] | 4     | b'1011      | BRANCH instruction code
**mode**                 | [22, 21] | 2     | [0, 4]      | The conditional branch mode
**false_pc**             | [20, 15] | 6     | [0, 63]     | Configures the false address
unused                   | [14, 0]  | 15    | 0           | Deprecated

### 1100 ROUTE

```
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  1  1  1  A  B  B  B  C  D  D  D  E  F  0  0  0  0  E  0  0  0  0  0  0  0  0
```

Field                    | Position | Width | Range/Value | Description
-------------------------|----------|-------|-------------|-------------------------
instr_code               | [26, 23] | 4     | b'1100      | ROUTE instruction code
**horizontal_dir**       | [22]     | 1     | [0, 1]      | 0: west, 1: east
**horizontal_hops**      | [21, 19] | 3     | [0, 7]      | number of hops - 1
**vertical_dir**         | [18]     | 1     | [0, 1]      | 0: south, 1: north
**vertical_hops**        | [17, 15] | 3     | [0, 7]      | number of hops - 1
**direction**            | [14]     | 1     | [0, 1]      | 0: read, 1: write
**select_drra_row**      | [13]     | 1     | [0, 1]      | DRRA row that sends the instruction
unused                   | [12, 0]  | 13    | 0           | Deprecated

### 1101 SRAM

```
80 79 78 77 76 75 74 73 72 71 70 69 68 67 66 65 64 63 62 61 60 59 58 57 56 55 54
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
1  1  0  1  A  B  B  B  B  B  B  B  C  C  C  C  D  D  D  D  D  D  D  E  E  E  E

53 52 51 50 49 48 47 46 45 44 43 42 41 40 39 38 37 36 35 34 33 32 31 30 29 28 27
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
E  E  E  E  F  F  F  F  F  F  G  G  G  G  G  G  G  H  H  H  H  H  H  H  H  I  I

26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
I  I  I  I  J  K  L  M  N  O  P  Q  R  R  R  R  S  0  0  0  0  0  0  0  0  0  0
```

Field                    | Position | Width | Range/Value | Description
-------------------------|----------|-------|-------------|-------------------------
instr_code               | [80, 77] | 4     | b'1101      | SRAMR instruction code
**rw**                   | [76]     | 1     | [0, 1]      | [0] Read [1] Write
**init_addr**            | [75, 69] | 7     | [0, 127]    | Initial address
init_delay               | [68, 65] | 4     | [0, 15]     | Initial delay
**l1_iter**              | [64, 58] | 7     | [0, 127]    | Level 1 iteration
**l1_step**              | [57, 50] | 8     | [-128, 127] | Level 1 step
l1_delay                 | [49, 44] | 6     | [0, 63]     | Level 1 delay
**l2_iter**              | [43, 37] | 7     | [0, 127]    | Level 2 iteration
**l2_step**              | [36, 29] | 8     | [-128, 127] | Level 2 step
l2_delay                 | [28, 23] | 6     | [0, 63]     | Level 2 delay
**init_addr_sd**         | [22]     | 1     | [0, 1]      | Static or from RACCU
**l1_iter_sd**           | [21]     | 1     | [0, 1]      | Static or from RACCU
**l2_iter_sd**           | [20]     | 1     | [0, 1]      | Static or from RACCU
init_delay_sd            | [19]     | 1     | [0, 1]      | Static or from RACCU
l1_delay_sd              | [18]     | 1     | [0, 1]      | Static or from RACCU
l2_delay_sd              | [17]     | 1     | [0, 1]      | Static or from RACCU
**l1_step_sd**           | [16]     | 1     | [0, 1]      | Static or from RACCU
**l2_step_sd**           | [15]     | 1     | [0, 1]      | Static or from RACCU
**hops**                 | [14, 11] | 4     | [0, 15]     | number of hops to the final dimarch cell - 1
unused                   | [10, 0]  | 11    | 0           | Unused

-->

## ISA Description File

### JSON Schema

The ISA description file uses *json* format and validated by the following json schema:

```json
--8<-- "Docs/ToolChain/Manas/InstructionSet/isa_schema.json"
```

!!! Note
    You can validate a ISA description json file using this schema on [Json Schema Validator](https://www.jsonschemavalidator.net/).

### JSON

The ISA description file used for DRRA is shown as follow:

```json
--8<-- "Docs/ToolChain/Manas/InstructionSet/isa.json"
```
