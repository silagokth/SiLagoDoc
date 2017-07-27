# Manus code

## Structure

Manus code is a kind of text-based assembly language for SiLago platform. Manus code is caseless.

### Segment

Manus code consists of segments. Two type of segments are valid in Manus code. They are **DATA SEGMENT** and **CODE SEGMENT** which are marked by ````.DATA```` and ````.CODE```` respectively. All the contents following a segment mark belong to that specific segment.

**DATA SEGMENT** is used for declare and initialize variables in register file, while **CODE SEGMENT** is used for assign instructions to *sequencer* of each DRRA cell.

### Variable

Variables should be defined in **DATA SEGMENT**. The defination format is:


````$variable_name distribution_type [<row0, col0>, <row1, col1>, ...] [element0, element1, ...]````

or

````$variable_name distribution_type [<row0, col0>, <row1, col1>, ...] ZEROS(number_of_element)````

or

````$variable_name distribution_type [<row0, col0>, <row1, col1>, ...] ONES(number_of_element)````

Variable name should always be led by a dollar (````$````) symbol. The first symbol after dollar (````$````) should only be underscore ( ````_```` ) or regular latin letter (a-z, A-Z). The following symbol can be underscore ( ````_```` ), regular latin letter (a-z, A-Z) as well as number (0-9).

Distribution type can be either __FULL\_DISTR__ or __EVEN\_DIRSR__.

The third argument is a list of DRRA cells.

The last argument is the list of initial value of each element inside the variable.

All variable are considered as a 1-D array in register file.

### DRRA cell

To specify a DRRA cell for following instructions, use:

````CELL <row, col>````

### Instructions

Instructions should be defined in **CODE SEGMENT**. The defination format is:

````instruction_name arg0 arg1 ...````

or

````instruction_name arg0, arg1, ...````

See next section for various instruction defination detail.

## Instructions set

### 0001 - REFI1

```
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  0  0  1  A  A  B  B  C  D  D  D  D  D  D  E  F  F  F  F  F  F  G  H  H  H  H
```

Field                | Position | Width | Range/Value | Description
---------------------|----------|-------|-------------|-------------------------
instr_code           | [26, 23] | 4     | b'0001      | REFI1 instruction code
reg_file_port        | [22, 21] | 2     | [0, 3]      | Selects one of the RFile ports
subseq_instrs        | [20, 19] | 2     | [0, 3]      | The instruction decoder fetches the consequent (REFI1) or (REFI1 and REFI2) instructions.
start_addrs_sd       | 18       | 1     | [0, 1]      | The start_addrs is valid only if the start_addrs_sd is 0. Otherwise the start_address would be taken from the RACCU register
start_addrs          | [17, 12] | 6     | [0, 63]     | Configures the starting address for the AGU
no_of_addrs_sd       | 11       | 1     | [0, 1]      | The no_of_addrs is valid only when no_of_addrs_sd is 0, otherwise the no_of_addrs would be taken from the RACCU register.
no_of_addrs          | [10, 5]  | 6     | [0, 63]     | Configures the number of address for the AGU
initial_delay_sd:    | 4        | 1     | [0, 1]      | The init_delay is valid only when init_delay_sd is set.
initial_delay        | [3, 0]   | 4     | [0, 15]     | Configures the initial delay

### 0010 - REFI2

```
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  0  1  0  A  B  B  B  B  B  B  C  D  E  E  E  E  F  G  G  G  G  G  H  H  H  H
```

Field                | Position | Width | Range/Value | Description
---------------------|----------|-------|-------------|-------------------------
instr_code           | [26, 23] | 4     | b'0010      | REFI2 instruction code
step_val_sd          | 22       | 1     | [0, 1]      | The step_val is valid only if step_val_sd is set.
step_val             | [21, 16] | 6     | [0, 63]     | Step incremental/decremental value of the address
step_val_sign        | 15       | 1     | [0, 1]      | If it's 0, address will be incremented by the step_val else decremented by the step_val
refi_middle_delay_sd | 14       | 1     | [0, 63]     | The refi_middle_delay is valid only if refi_middle_delay_sd is 0
refi_middle_delay    | [13, 10] | 4     | [0, 15]     | Configures the middle dealy
no_of_reps_sd        | 9        | 1     | [0, 1]      | The no_of_reps is valid only when the no_of_reps_sd is 0, otherwise the no_of_reps value would be taken from the RACCU register
no_of_reps           | [8, 4]   | 5     | [0, 31]     | Configures the number of times the address pattern to repeat
rpt_step_value       | [3, 0]   | 4     | [0, 15]     | The step increment/decrement value

### 0011 - REFI3

### 0100 - DPU

```
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  1  0  0  A  A  A  A  A  B  B  C  C  D  D  E  F  G  G  G  G  G  G  G  G  H  H
```

Field                | Position | Width | Range/Value | Description
---------------------|----------|-------|-------------|-------------------------
instr_code           | [26, 23] | 4     | b'0100      | DPU instruction code
dpu_mode             | [22, 18] | 5     | [0, 12]     | Configures the valid dpu mode
dpu_saturation       | [17, 16] | 2     | [0, 3]      | Selects the integer or fixed point operation with or without saturation [^dpu-saturation]
dpu_output_a         | [15, 14] | 2     | [0, 3]      | Selects one or both the outports [^dpu-output-ports]
dpu_output_b         | [13, 12] | 2     | [0, 3]      | Selects one or both the outports [^dpu-output-ports]
dpu_acc_clear_rst    | 11       | 1     | [0, 1]      | Asynchronous reset, when set, clears the DPU accumulator register.
dpu_acc_clear_sd     | 10       | 1     | [0, 1]      | The dpu_acc_clear is valid only when dpu acc_clear_sd is set.
dpu_acc_clear        | [9, 2]   | 8     | [0, 255]    | The DPU accumulator register is cleared when the accumulator counter reaches the value configured in the dpu_acc_clear
dpu_process_inout    | [1, 0]   | 2     | [0, 3]      | Processes the input or output [^dpu-process-ports]

[^dpu-saturation]:
**Saturation**:
[0]: Integer operation without saturation;
[1]: Fixed point without saturation;
[2]: Integer operation with saturation;
[3]: Fixed point with saturation;

[^dpu-output-ports]:
**Output ports**:
[0]: Disables both out ports;
[1]: Out port 0 will be enabled;
[2]: Out port 1 will be enabled;
[3]: Both Out port 0 and 1 will be enabled;

[^dpu-process-ports]:
**Process ports**:
[0]: No preprocessing;
[1]: Negates input 0;
[2]: Negates input 1;
[3]: Absolute of (in0-in1);

### 0101 - SWB

!!! info
	The actual 27-bit machine code is automatically calculated by the fabric while doing simulation or by the compiler.

Field                | Position | Width | Range/Value | Description
---------------------|----------|-------|-------------|-------------------------
instr_code           | N/A      | N/A   | b'0101      | SWB instruction code
from_block           | N/A      | N/A   | N/A         | The source block[^block]
from_address         | N/A      | N/A   | N/A         | The source unit address[^addr]
from_port            | N/A      | N/A   | N/A         | The source port
to_block             | N/A      | N/A   | N/A         | The destination block[^block]
to_address           | N/A      | N/A   | N/A         | The destination unit address[^addr]
to_port              | N/A      | N/A   | N/A         | The destination port

[^block]:
This block refers to the alias of register file or datapath unit.
[0] Register file
[1] Datapath unit

[^addr]:
This address is calculated according to $addr=row+col*2$. Since in standard fabric,
maxtimum continued row is 2.

### 0110 - JUMP

```
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  1  1  0  A  A  A  A  A  A  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -
```

Field                | Position | Width | Range/Value | Description
---------------------|----------|-------|-------------|-------------------------
instr_code           | [26, 23] | 4     | b'0110      | JUMP instruction code
true_addrs           | [22, 17] | 6[^pc_size]     | [0, 63]     | The target address
N/A                  | [16, 0]  | 17    | N/A         | N/A

[^pc_size]:
This number equals to PC size, which is $log_2(DEPTH_{instr})$. By default, $DEPTH_{instr} = 64$.

### 0111 - DELAY

```
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  1  1  1  A  B  B  B  B  B  B  B  B  B  B  B  B  B  B  B  -  -  -  -  -  -  -
```

Field                | Position | Width | Range/Value | Description
---------------------|----------|-------|-------------|-------------------------
instr_code           | [26, 23] | 4     | b'0111      | DELAY instruction code
del_cycles_sd        | 22       | 1     | [0, 1]      | The del_cycles is valid only if del_cycles_sd is set
del_cycles           | [21, 7]  | 15    | [0, 32767]  | Number of clock cycles to wait before decoding the next instruction
N/A                  | [6, 0]   | 7     | N/A         | N/A

### 1000 - FOR_HEADER

### 1001 - FOR_TAIL

### 1010 - RACCU

### 1011 - BRANCH

```
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
0  1  1  1  A  A  B  B  B  B  B  B  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -
```

Field                | Position | Width | Range/Value | Description
---------------------|----------|-------|-------------|-------------------------
instr_code           | [26, 23] | 4     | b'1011      | BRANCH instruction code
brnch_mode           | [22, 21] | 2     | [0, 4]      | The conditional branch jumps to the false address when the (brnch_mode && seq_cond_status) == "00"
brnch_false_addr     | [20, 15] | 6[^pc_size]     | [0, 63]     | Configures the false address
N/A                  | [14, 0]  | 15    | N/A         | N/A

### 1100 - ROUTE

### 1101 - SRAM_READ

### 1110 - SRAM_WRITE

### 1111 - HALT[^halt]

```
26 25 24 23 22 21 20 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01 00
|  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
1  1  1  1  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -
```

Field                | Position | Width | Range/Value | Description
---------------------|----------|-------|-------------|-------------------------
instr_code           | [26, 23] | 4     | b'1111      | HALT instruction code
N/A                  | [22, 0]  | 23    | N/A         | N/A

[^halt]: This instruction is not used.

## Example

### Matlab code

```matlab
A = [3 : 12]; %! RFILE<> [0,0]
B = [5 : 14]; %! RFILE<> [0,0]
C = [0];      %! RFILE<> [0,0]

C(1) = A(1) + B(1); %! DPU [0,0]
```

### Manus assembly code

````
.DATA
$A FULL_DISTR [<0,0>] [3,4,5,6,7,8,9,10,11,12]
$B FULL_DISTR [<0,0>] [5,6,7,8,9,10,11,12,13,14]
$C FULL_DISTR [<0,0>] [0]

.CODE
CELL    <0,0>
SWB     0  0  3  1  0  2
SWB     0  0  2  1  0  3
SWB     1  0  0  0  0  1
REFI1   3  0  0  0  0  0  0  2
REFI1   2  0  0  10 0  0  0  1
DPU     10 0  3  3  1  0  0  0
REFI1   1  0  0  20 0  0  0  0
````

## Limitations

- Doesn't support fixed-point representation, you need to convert fixed-point to integer format manually.
- Doesn't support DiMarch variable declaration.
- Doesn't support Input and Output variable type, only Temporary variable type is valid.
- No static time model analysis. The execution cycle is by default set to 1000.

