
!!! Note
    The slot number will decide which data port it configures.

## Instruction Format
Instructions are 32-bit wide. The MSB indicates whether it's a control instruction or a data instruction. [0]: control; [1]: data; The rest of the bits are used to encode the instruction content. For data instructions, the first 4 bits in the instruction content are used to indicate the slot number. The rest of the bits are used to encode the instruction content.

## Control Instructions

Field | Position | Width | Description
------|----------|-------|-------------------------
instr_code | [31, 28] | 4 | Instruction code. The MSB indicates whether it's a control instruction or a data instruction. [0]: control; [1]: data;
instr_content | [27, 0] | 27 | The content of the instruction. The meaning of this field depends on the instruction type and instruction code.

### 0000 WAIT

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 0 | Instruction code for WAIT
**cycle** | [27, 0] | 28 | 0 | Number of cycles. If cycle=0, then the WAIT instruction will wait forever.

### 0001 ACT

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 1 | Instruction code for ACT
**slots** | [27, 12] | 16 | 0 | 1-hot encoded slots that need to be activated.

### 0010 CALC

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 2 | Instruction code for CALC
**mode** | [27, 24] | 4 | 0 | Calculation mode [0]:idle; [1]:add; [2]:sub; [3]:shift_r; [4]:shift_l; [5]:mult; [6]:div; [7]:mod;
**operand1_sd** | [23, 23] | 1 | 0 | Is the first operand static or dynamic? [0]:s; [1]:d;
**operand1** | [22, 14] | 9 | 0 | First operand.
**operand2_sd** | [13, 13] | 1 | 0 | Is the second operand static or dynamic? [0]:s; [1]:d;
**operand2** | [12, 4] | 9 | 0 | Second operand.
**result** | [3, 0] | 4 | 0 | The RACCU register to store the result.

### 0011 LOOP

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 3 | Instruction code for LOOP
**loopid** | [27, 26] | 2 | 0 | The id of the loop manager slot.
**endpc** | [25, 19] | 7 | 0 | The PC where loop ends. It's relative to the current PC.
**start_sd** | [18, 18] | 1 | 0 | Is the start static or dynamic? [0]:s; [1]:d;
**start** | [17, 12] | 6 | 0 | The start of iterator.
**iter** | [11, 6] | 6 | 0 | The number of iteration.
**step** | [5, 0] | 6 | 1 | The iteration step.

### 0100 BRN

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 4 | Instruction code for BRN
**mode** | [27, 25] | 3 | 0 | The branch mode: [0]: always; [1]: equal; [2]: not_equal; [3]: greater; [4]: less; [5]: greater_equal; [6]: less_equal;
**pc** | [24, 18] | 7 | 0 | The PC to jump to in case the condition is true. The PC is relative to the current PC.
**slot** | [17, 14] | 4 | 0 | The slot that will provide the flags.

### 0101 SWB

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 5 | Instruction code for SWB
**channel** | [27, 24] | 4 | 0 | Bus channel. Note: if the SWB is implemented by a crossbar, the channel is always equals to the target slot.
**source** | [23, 20] | 4 | 0 | Source slot.
**target** | [19, 16] | 4 | 0 | Target slot

### 0110 ROUTE

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 5 | Instruction code for SWB
**sr**     | [27, 27] | 1 | 0 | Send or receive. [0]: send; [1]: receive;
**direction** | [26, 23] | 4 | 0 | 1-hot encoded direction: E/N/W/S. If it's a receive instruction, the direction can only have 1 bit set to 1.
**slot** | [22, 7] | 16 | 0 | 1-hot encoded slot number. If it's a send instruction, the slot can only have 1 bit set to 1.

## Data Instructions

Field | Position | Width | Description
------|----------|-------|-------------------------
instr_code | [31, 28] | 4 | Instruction code. The MSB indicates whether it's a control instruction or a data instruction. [0]: control; [1]: data;
slot | [27, 24] | 4 | The slot number.
instr_content | [23, 0] | 24 | The content of the instruction. The meaning of this field depends on the instruction type and instruction code.

!!! Note
    When instruction code start with "111", the instruction contains a field that need to be replaced by RACCU registers if the filed is marked "dynamic".

Slot Type | Supported Instructions
------|-------------------------
Register File | MASK DSU, REP
SRAM Block | MASK, DSU, REP
IO Block | MASK, DSU, REP
DPU | DPU, REP

### 1000 REP

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 8 | Instruction code for REP
**slot**  | [27, 24] | 4 | N/A | Slot number.
**level** | [23, 20] | 4 | 0 | The level of the REP instruction. [0]: inner most level, [15]: outer most level.
**iter** | [19, 14] | 6 | 0 | iteration - 1.
**step** | [13, 8] | 6 | 0 | iteration step. This field is only useful when paired with REFI/SRAM instructions.
**delay** | [7, 0] | 8 | 0 | Repetition delay.

### 1001 MASK

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 9 | Instruction code for MASK
**slot**  | [27, 24] | 4 | N/A | Slot number.
**chunk** | [23, 21] | 3 | 0 | Mask chunk of 16 elements. If each element is 16-bit, only 1 chunk is needed. If each element is 8-bit, 2 chunks are needed. If each element is 4-bit, 4 chunks are needed. If each element is 2-bit, 8 chunks are needed.
**mask** | [20, 5] | 16 | 0 | The mask of 16-elements. If mask-bit is 0, then the corresponding element is useful and will be written to destination memory block. If mask-bit is 1, then the corresponding element is not useful and will be ignored.

### 1010 DPU

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 10 | Instruction code for DPU
**slot**  | [27, 24] | 4 | N/A | Slot number.
**bw** | [23, 22] | 2 | 0 | The bit-width mode: [0]: 16-bit; [1]: 8-bit; [2]: 4-bit; [3]: 2-bit;
**mode** | [21, 17] | 5 | 0 | The DPU mode. [0]:idle; [1]:add; [2]:sum_acc; [3]:add_const; [4]:subt; [5]:subt_abs; [6]:mode_6; [7]:mult; [8]:mult_add; [9]:mult_const; [10]:mac; [11]:ld_ir; [12]:axpy; [13]:max_min_acc; [14]:max_min_const; [15]:mode_15; [16]:max_min; [17]:shift_l; [18]:shift_r; [19]:sigm; [20]:tanhyp; [21]:expon; [22]:lk_relu; [23]:relu; [24]:div; [25]:acc_softmax; [26]:div_softmax; [27]:ld_acc; [28]:scale_dw; [29]:scale_up; [30]:mac_inter; [31]:mode_31;
**immediate** | [16, 1] | 16 | 0 | The immediate field used by some DPU modes.

### 1110 DSU

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 14 | Instruction code for DSU
**slot**  | [27, 24] | 4 | N/A | Slot number.
**init_addr_sd** | [23, 23] | 1 | 0 | Is init_addr static or dynamic? [0]:s; [1]:d;
**init_addr** | [22, 7] | 16 | 0 | Initial address.
**rw** | [6, 6] | 1 | 0 | Read or Write. [0]:r; [1]:w;
**rw** | [5, 5] | 1 | 0 | The port type. [0]: narrow (SWB); [1]: wide (ROUTE);
**level** | [4, 1] | 4 | 0 | The level of that the init address should be applied on. [0]: inner most level, [15]: outer most level.