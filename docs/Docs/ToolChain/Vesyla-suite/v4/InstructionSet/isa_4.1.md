
### Instruction Format
Instructions are 32-bit wide. The MSB indicates whether it's a control instruction or a resource instruction. [0]: control; [1]: resource; The first 4-bit (bit 32~28) is the instruction code. The rest of the bits are used to encode the instruction content. For resource instructions, bit 27~24 in the instruction content are used to indicate the slot number. The rest of the bits are used to encode the instruction content.

### Control Instructions

Field | Position | Width | Description
------|----------|-------|-------------------------
instr_code | [31, 28] | 4 | Instruction code. The MSB indicates whether it's a control instruction or a resource instruction. [0]: control; [1]: data;
instr_content | [27, 0] | 27 | The content of the instruction. The meaning of this field depends on the instruction type and instruction code.

#### 0000 halt
Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 0 | Instruction code for halt. The halt instruction will stop the execution of the controller.

#### 0001 wait

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 1 | Instruction code for wait
**mode** | [27, 27] | 1 | 0 | Wait mode: [0]: wait for a number of cycles; [1]: wait for the completion of task in slot X, X is defined by the cycle field using 1-hot encoding;
**cycle** | [26, 0] | 28 | 0 | if mode is 0, the wait instruction waits for **cycle** extra cycles excluding the current cycle used for the wait instruction. If mode is 1, it waits for the sensitive slots defined by 1-hot encoded **cycle**.

#### 0010 act
!!! note
    Mode 2 is not supported for now.

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 2 | Instruction code for act
**ports** | [27, 12] | 16 | 0 | 1-hot encoded ports that need to be activated. There are 64 ports in total, but only 16 can be activated at the same time. The ports are filtered by the mode and parameter field.
**mode** | [11, 8] | 4 | 0 | Filter mode: [0]: Continues ports start from slot X; [1] All port X in each slot; [2]: the predefined 64-bit activation code in internal activation memory location X.
**param** | [7, 0] | 8 | 0 | The parameter for the filter mode.

#### 0011 calc

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 3 | Instruction code for CALC
**mode** | [27, 24] | 4 | 0 | Calculation mode. [0]:idle; [1]:add; [2]:sub; [3]:lls; [4]:lrs; [5]:mul; [6]:div; [7]:mod; [8]:bit-and; [9]:bit-or; [10]:bit-inv; [11]:bit-xor; [17]:eq; [18]:ne; [19]:gt; [20]:ge; [21]:lt; [22]:le; [32]:and; [33]:or; [34]:not;
**operand1** | [23, 20] | 4 | 0 | First operand.
**operand2_sd** | [19, 19] | 1 | 0 | Is the second operand static or dynamic? [0]:s; [1]:d;
**operand2** | [18, 11] | 8 | 0 | Second operand.
**result** | [10, 7] | 4 | 0 | The register to store the result. If it's scalar operation, the result is stored in the scalar register. If it's logic operations, the result is stored in the bool register.

#### 0100 brn

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 4 | Instruction code for brn
**reg** | [27, 24] | 4 | 0 | The bool register to check
**target_true** | [23, 15] | 9 | 0 | The relative pc offset if the bool register is true.
**target_false** | [14, 6] | 9 | 0 | The relative pc offset if the bool register is false.


### Resource Instructions

Field | Position | Width | Description
------|----------|-------|-------------------------
instr_code | [31, 28] | 4 | Instruction code. The MSB indicates whether it's a control instruction or a resource instruction. [0]: control; [1]: data;
slot | [27, 24] | 4 | The slot number.
instr_content | [23, 0] | 24 | The content of the instruction. The meaning of this field depends on the instruction type and instruction code.

!!! Note
    When instruction code start with "111", the instruction contains a field that need to be replaced by RACCU registers if the filed is marked "dynamic".

Slot Type | Supported Instructions
------|-------------------------
Register File | DSU, REP, REPX
SRAM Block | DSU, REP, REPX
IO Block | DSU, REP, REPX
DPU | DPU, REP, REPX, FSM

#### 1000 rep

This instruction is accepted by the following slots:

- swb
- iosram
- dpu
- rf
- sram

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 8 | Instruction code for REP
**slot**  | [27, 24] | 4 | N/A | Slot number.
**port**  | [23, 21]  | 2 | 0 | The port number. [0]: write narrow; [1]: read narrow; [2]: write wide; [3]: read wide;
**level** | [20, 17] | 4 | 0 | The level of the REP instruction. [0]: inner most level, [15]: outer most level.
**iter**  | [16, 11] | 6 | 0 | iteration - 1.
**step**  | [10, 5] | 6 | 0 | iteration step. This field is only useful when paired with REFI/SRAM instructions.
**delay** | [5, 0] | 6 | 0 | Repetition delay.

#### 1001 repx

This instruction is accepted by the following slots:

- swb
- iosram
- dpu
- rf
- sram

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 9 | Instruction code for REPX. Configure the higher 6-bit of each field in the REP instruction.
**slot**  | [27, 24] | 4 | N/A | Slot number.
**port**  | [23, 21]  | 2 | 0 | The port number. [0]: write narrow; [1]: read narrow; [2]: write wide; [3]: read wide;
**level** | [20, 17] | 4 | 0 | The level of the REP instruction. [0]: inner most level, [15]: outer most level.
**iter**  | [16, 11] | 6 | 0 | iteration - 1.
**step**  | [10, 5] | 6 | 0 | iteration step. This field is only useful when paired with REFI/SRAM instructions.
**delay** | [5, 0] | 6 | 0 | Repetition delay.

#### 1010 fsm

This instruction is accepted by the following slots:

- swb
- dpu

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 10 | Instruction code for FSM
**slot**  | [27, 24] | 4 | N/A | Slot number.
**port**  | [23, 21] | 3 | 0 | The port number. [0]: write narrow; [1]: write narrow; [2]: write wide; [3]: read wide;
**delay_0** | [20, 14] | 7 | 0 | The delay between state 0 and 1.
**delay_1** | [13, 7] | 7 | 0 | The delay between state 1 and 2.
**delay_2** | [6, 0] | 7 | 0 | The delay between state 2 and 3.


#### 1011 dpu

This instruction is accepted by the following slots:

- dpu

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 11 | Instruction code for DPU
**slot**  | [27, 24] | 4 | N/A | Slot number.
**option** | [23, 22] | 2 | 0 | Programmable FSM states. Max 4 states.
**mode** | [21, 17] | 5 | 0 | The DPU mode. [0]:idle; [1]:add; [2]:sum_acc; [3]:add_const; [4]:subt; [5]:subt_abs; [6]:mode_6; [7]:mult; [8]:mult_add; [9]:mult_const; [10]:mac; [11]:ld_ir; [12]:axpy; [13]:max_min_acc; [14]:max_min_const; [15]:mode_15; [16]:max_min; [17]:shift_l; [18]:shift_r; [19]:sigm; [20]:tanhyp; [21]:expon; [22]:lk_relu; [23]:relu; [24]:div; [25]:acc_softmax; [26]:div_softmax; [27]:ld_acc; [28]:scale_dw; [29]:scale_up; [30]:mac_inter; [31]:mode_31;
**immediate** | [16, 1] | 16 | 0 | The immediate field used by some DPU modes.

#### 1100 swb

This instruction is accepted by the following slots:

- swb

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 12 | Instruction code for SWB
**slot**  | [27, 24] | 4 | N/A | Slot number.
**option** | [23, 22] | 2 | 0 | Programmable FSM states. Max 4 states.
**channel** | [21, 18] | 4 | 0 | Bus channel. Note: if the SWB is implemented by a crossbar, the channel is always equals to the target slot.
**source** | [17, 14] | 4 | 0 | Source slot.
**target** | [13, 10] | 4 | 0 | Target slot

#### 1101 route

This instruction is accepted by the following slots:

- swb

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 13 | Instruction code for ROUTE
**slot**  | [27, 24] | 4 | N/A | Slot number.
**option** | [23, 22] | 2 | 0 | Programmable FSM states. Max 4 states.
**sr**     | [21, 21] | 1 | 0 | Send or receive. [0]: send; [1]: receive;
**source** | [20, 17] | 4 | 0 | 1-hot encoded direction: E/N/W/S. If it's a receive instruction, the direction can only have 1 bit set to 1.
**target** | [16, 1] | 16 | 0 | 1-hot encoded slot number. If it's a send instruction, the slot can only have 1 bit set to 1.


#### 1110 dsu

This instruction is accepted by the following slots:

- iosram
- rf
- sram

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [31, 28] | 4 | 14 | Instruction code for DSU
**slot**  | [27, 24] | 4 | N/A | Slot number.
**init_addr_sd** | [23, 23] | 1 | 0 | Is init_addr static or dynamic? [0]:s; [1]:d;
**init_addr** | [22, 7] | 16 | 0 | Initial address.
**port** | [6, 5] | 2 | 0 | The port number. [0]: write_narrow; [1]: read_narrow; [2]: write_wide; [3]: read_wide; If it's applied to IO, then [0]: read from IO to SRAM; [1]: write from SRAM to IO;