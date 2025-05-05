

# ISA Specification

Instructions are 32-bit wide. The MSB indicates whether it's a control instruction or a resource instruction. [0]: control; [1]: resource; The next  bits represent the instruction opcode. The rest of the bits are used to encode the instruction content. For resource instructions, another  bits in the instruction content are used to indicate the slot number. The rest of the bits are used to encode the instruction content.

Note that, specifically for resource instructions, if instruction opcode start with "11", the instruction contains a field that need to be replaced by RACCU registers if the filed is marked "dynamic".

## ISA Format
Parameter | Width | Description
----------|-------|-------------------------
instr_bitwidth | 32 | Instruction bitwidth
instr_type_bitwidth | 1 | Instruction type bitwidth
instr_opcode_bitwidth | 3 | Instruction opcode bitwidth
instr_slot_bitwidth | 4 | Instruction slot bitwidth, only used for resource components

## Instructions For Each Component

### sequencer ( controller )



#### halt [opcode=0]

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------

#### wait [opcode=1]

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
mode | [27, 27] | 1 |0| Wait mode, 0 means wait for a number of cycles, 1 means wait for events.
cycle | [26, 0] | 27 |0| If mode is 0, it means the extra cycles to wait excluding the current cycle when this wait instruction is issued. If the mode is 1, this is the 1-hot encoding of event slots.

#### act [opcode=2]

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
ports | [27, 12] | 16 |0| 1-hot encoded slots that need to be activated.
mode | [11, 8] | 4 |0| Filter mode: [0]: Continues ports start from slot X; [1] All port X in each slot; [2]: the predefined 64-bit activation code in internal activation memory location X.
param | [7, 0] | 8 |0| The parameter for the filter mode.

#### calc [opcode=3]

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
mode | [27, 22] | 6 |0| Calculation mode [0]:idle; [1]:add; [2]:sub; [3]:lls; [4]:lrs; [5]:mul; [6]:div; [7]:mod; [8]:bitand; [9]:bitor; [10]:bitinv; [11]:bitxor; [17]:eq; [18]:ne; [19]:gt; [20]:ge; [21]:lt; [22]:le; [32]:and; [33]:or; [34]:not;
operand1 | [21, 18] | 4 |0| First operand.
operand2_sd | [17, 17] | 1 |0| Is the second operand static or dynamic? [0]:s; [1]:d;
operand2 | [16, 9] | 8 |0| Second operand.
result | [8, 5] | 4 |0| The register to store the result. It could be the scalar register or flag register, depending on the calculation mode.

#### brn [opcode=4]

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
reg | [27, 24] | 4 |0| The flag register
target_true | [23, 15] | 9 |0| The PC to jump to in case the condition is true. The PC is relative to the current PC.
target_false | [14, 6] | 9 |0| The PC to jump to in case the condition is false. The PC is relative to the current PC.

### swb ( resource )



#### swb [opcode=4]

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
option | [23, 22] | 2 |0| configuration option
channel | [21, 18] | 4 |0| Bus channel. Note: if the SWB is implemented by a crossbar, the channel is always equals to the target slot.
source | [17, 14] | 4 |0| Source slot.
target | [13, 10] | 4 |0| Target slot.

#### route [opcode=5]

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
option | [23, 22] | 2 |0| configuration option
sr | [21, 21] | 1 |0| Send or receive. [0]:s; [1]:r;
source | [20, 17] | 4 |0| 1-hot encoded direction: E/N/W/S. If it's a receive instruction, the direction can only have 1 bit set to 1.
target | [16, 1] | 16 |0| 1-hot encoded slot number. If it's a send instruction, the slot can only have 1 bit set to 1.

### iosram_top ( resource )



#### dsu [opcode=6]

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
init_addr_sd | [23, 23] | 1 |0| Is initial address static or dynamic? [0]:s; [1]:d;
init_addr | [22, 7] | 16 |0| Initial address
port | [6, 5] | 2 |0| The port number [0]:read narrow; [1]:read wide; [2]:write narrow; [3]:write wide;

#### rep [opcode=0]

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
port | [23, 22] | 2 |0| The port number [0]:read narrow; [1]:read wide; [2]:write narrow; [3]:write wide;
level | [21, 18] | 4 |0| The level of the REP instruction. [0]: inner most level, [15]: outer most level.
iter | [17, 12] | 6 |0| level-1 iteration - 1.
step | [11, 6] | 6 |1| level-1 step
delay | [5, 0] | 6 |0| delay

#### repx [opcode=1]

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
port | [23, 22] | 2 |0| The port number [0]:read narrow; [1]:read wide; [2]:write narrow; [3]:write wide;
level | [21, 18] | 4 |0| The level of the REP instruction. [0]: inner most level, [15]: outer most level.
iter | [17, 12] | 6 |0| level-1 iteration - 1.
step | [11, 6] | 6 |1| level-1 step
delay | [5, 0] | 6 |0| delay

### rf ( resource )



#### dsu [opcode=6]

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
init_addr_sd | [23, 23] | 1 |0| Is initial address static or dynamic? [0]:s; [1]:d;
init_addr | [22, 7] | 16 |0| Initial address
port | [6, 5] | 2 |0| The port number [0]:read narrow; [1]:read wide; [2]:write narrow; [3]:write wide;

#### rep [opcode=0]

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
port | [23, 22] | 2 |0| The port number [0]:read narrow; [1]:read wide; [2]:write narrow; [3]:write wide;
level | [21, 18] | 4 |0| The level of the REP instruction. [0]: inner most level, [15]: outer most level.
iter | [17, 12] | 6 |0| level-1 iteration - 1.
step | [11, 6] | 6 |1| level-1 step
delay | [5, 0] | 6 |0| delay

#### repx [opcode=1]

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
port | [23, 22] | 2 |0| The port number [0]:read narrow; [1]:read wide; [2]:write narrow; [3]:write wide;
level | [21, 18] | 4 |0| The level of the REP instruction. [0]: inner most level, [15]: outer most level.
iter | [17, 12] | 6 |0| level-1 iteration - 1.
step | [11, 6] | 6 |1| level-1 step
delay | [5, 0] | 6 |0| delay

### dpu ( resource )



#### dpu [opcode=3]

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
option | [23, 22] | 2 |0| Configuration option.
mode | [21, 17] | 5 |0| The DPU mode.  [0]:idle; [1]:add; [2]:sum_acc; [3]:add_const; [4]:subt; [5]:subt_abs; [6]:mode_6; [7]:mult; [8]:mult_add; [9]:mult_const; [10]:mac; [11]:ld_ir; [12]:axpy; [13]:max_min_acc; [14]:max_min_const; [15]:mode_15; [16]:max_min; [17]:shift_l; [18]:shift_r; [19]:sigm; [20]:tanhyp; [21]:expon; [22]:lk_relu; [23]:relu; [24]:div; [25]:acc_softmax; [26]:div_softmax; [27]:ld_acc; [28]:scale_dw; [29]:scale_up; [30]:mac_inter; [31]:mode_31;
immediate | [16, 1] | 16 |0| The immediate field used by some DPU modes.

#### rep [opcode=0]

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
port | [23, 22] | 2 |0| The port number [0]:read narrow; [1]:read wide; [2]:write narrow; [3]:write wide;
level | [21, 18] | 4 |0| The level of the REP instruction. [0]: inner most level, [15]: outer most level.
iter | [17, 12] | 6 |0| level-1 iteration - 1.
step | [11, 6] | 6 |1| level-1 step
delay | [5, 0] | 6 |0| delay

#### repx [opcode=1]

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
port | [23, 22] | 2 |0| The port number [0]:read narrow; [1]:read wide; [2]:write narrow; [3]:write wide;
level | [21, 18] | 4 |0| The level of the REP instruction. [0]: inner most level, [15]: outer most level.
iter | [17, 12] | 6 |0| level-1 iteration - 1.
step | [11, 6] | 6 |1| level-1 step
delay | [5, 0] | 6 |0| delay

#### fsm [opcode=2]

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
port | [23, 22] | 2 |0| The port number
delay_0 | [21, 15] | 7 |0| Delay between state 0 and 1.
delay_1 | [14, 8] | 7 |0| Delay between state 1 and 2.
delay_2 | [7, 1] | 7 |0| Delay between state 2 and 3.

### iosram_btm ( resource )



#### dsu [opcode=6]

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
init_addr_sd | [23, 23] | 1 |0| Is initial address static or dynamic? [0]:s; [1]:d;
init_addr | [22, 7] | 16 |0| Initial address
port | [6, 5] | 2 |0| The port number [0]:read narrow; [1]:read wide; [2]:write narrow; [3]:write wide;

#### rep [opcode=0]

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
port | [23, 22] | 2 |0| The port number [0]:read narrow; [1]:read wide; [2]:write narrow; [3]:write wide;
level | [21, 18] | 4 |0| The level of the REP instruction. [0]: inner most level, [15]: outer most level.
iter | [17, 12] | 6 |0| level-1 iteration - 1.
step | [11, 6] | 6 |1| level-1 step
delay | [5, 0] | 6 |0| delay

#### repx [opcode=1]

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
port | [23, 22] | 2 |0| The port number [0]:read narrow; [1]:read wide; [2]:write narrow; [3]:write wide;
level | [21, 18] | 4 |0| The level of the REP instruction. [0]: inner most level, [15]: outer most level.
iter | [17, 12] | 6 |0| level-1 iteration - 1.
step | [11, 6] | 6 |1| level-1 step
delay | [5, 0] | 6 |0| delay