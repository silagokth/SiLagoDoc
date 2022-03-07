

### HALT

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [26, 23] | 4 | 0 | Instruction code for HALT

### REFI

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [80, 77] | 4 | 1 | Instruction code for REFI
port_no | [80, 78] | 2 | 0 | Selects one of the RFile port. [0]:w0; [1]:w1; [2]:r0; [3]:r1;
extra | [82, 80] | 2 | 0 | How many following chunks?
init_addr_sd | [84, 83] | 1 | 0 | Is init_addr static or dymamic? [0]:s; [1]:d;
init_addr | [85, 79] | 6 | 0 | Initial address.
l1_iter | [91, 85] | 6 | 0 | Level-1 iteration - 1.
init_delay | [97, 91] | 6 | 0 | Initial delay.
l1_iter_sd | [103, 102] | 1 | 0 | Is level-1 iteration static or dymamic? [0]:s; [1]:d;
init_delay_sd | [104, 103] | 1 | 0 | Is initial delay static or dynamic? [0]:s; [1]:d;
unused_0 | [105, 103] | 2 | 2 | Deprecated.
l1_step_sd | [107, 106] | 1 | 0 | Is level-1 step static or dynamic? [0]:s; [1]:d;
l1_step | [108, 102] | 6 | 1 | Level-1 step
l1_step_sign | [114, 113] | 1 | 0 | The sign of level-1 step. [0]:+; [1]:-;
l1_delay_sd | [115, 114] | 1 | 0 | Is the level-1 delay static or dynamic? [0]:s; [1]:d;
l1_delay | [116, 112] | 4 | 0 | The level-1 delay, middle delay
l2_iter_sd | [120, 119] | 1 | 0 | Is level-2 iteration static or dymamic? [0]:s; [1]:d;
l2_iter | [121, 116] | 5 | 0 | The level-2 iteration.
l2_step | [126, 122] | 4 | 1 | The level-2 step.
unused_1 | [130, 126] | 4 | 3 | Deprecated.
l2_delay_sd | [134, 133] | 1 | 0 | Is the level-2 delay static or dynamic? [0]:s; [1]:d;
l2_delay | [135, 129] | 6 | 0 | The level-2 delay, repetition delay.
unused_2 | [141, 135] | 6 | 0 | Deprecated.
l1_delay_ext | [147, 145] | 2 | 0 | The extened bits near MSB of l1_delay.
l2_iter_ext | [149, 148] | 1 | 0 | The extened bits near MSB of l2_iter.
l2_step_ext | [150, 148] | 2 | 0 | The extened bits near MSB of l2_step.
unused_3 | [152, 149] | 3 | 0 | Deprecated.
dimarch | [155, 154] | 1 | 0 | Is reading/writing from/to DiMArch? [0]:n; [1]:y;
compress | [156, 155] | 1 | 0 | Is the data compressed? [0]:n; [1]:y;

### DPU

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [26, 23] | 4 | 4 | Instruction code for DPU
mode | [26, 21] | 5 | 0 | The DPU mode. [0]:idle; [1]:add; [2]:add_3; [3]:add_acc; [4]:add_const; [5]:subt; [6]:subt_abs; [7]:subt_acc; [8]:mult; [9]:mult_add; [10]:mult_const; [11]:mac; [12]:som_dist; [13]:mac_const; [14]:max_acc; [15]:max_const; [16]:min_acc; [17]:max; [18]:shift_l; [19]:shift_r; [20]:sigm; [21]:tanhyp; [22]:expon; [23]:lk_relu; [24]:elu; [25]:div; [26]:acc_softmax; [27]:div_softmax; [28]:ld_regs; [29]:st_regs; [30]:change_q;
control | [31, 29] | 2 | 2 | The controll mode: saturation and operator type. [0]:nosat_int; [1]:nosat_fx; [2]:sat_int; [3]:sat_fx;
unused_0 | [33, 27] | 6 | 2 | Deprecated.
acc_clear | [39, 31] | 8 | 0 | The accumulator clear signal will be triggered if the accumulation reaches this number. It also serves as immediate value for some DPU mode.
io_change | [47, 45] | 2 | 0 | The IO mode: negate input and absolute output. [0]:no_change; [1]:negate_in0; [2]:negate_in1; [3]:abs_out;

### SWB

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [26, 23] | 4 | 5 | Instruction code for SWB
unused0 | [26, 25] | 1 | 1 | Deprecated.
src_row | [27, 26] | 1 | 0 | Source row.
src_block | [28, 27] | 1 | 0 | Source block, RF or DPU. [0]:rf; [1]:dpu;
src_port | [29, 28] | 1 | 0 | source port.
hb_index | [30, 27] | 3 | 0 | Index of horizontal bus. This is the column difference of the src and dest cell shifting by 2. For example if the path is from [0,0] to [1,2], the column difference is -2, so the hb_index = -2+2=0.
send_to_other_row | [33, 32] | 1 | 0 | Flag of whether src and dest row are equal. [0]:y; [1]:n;
v_index | [34, 31] | 3 | 0 | Index of vertical bus. This is the dest port. If destination is RF, the v_index is the port number, if the dest is DPU, the v_index is port number + 2.

### JUMP

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [26, 23] | 4 | 6 | Instruction code for JUMP
pc | [26, 20] | 6 | 0 | The PC to jump to

### WAIT

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [26, 23] | 4 | 7 | Instruction code for WAIT
cycle_sd | [26, 25] | 1 | 0 | Is the cycle static or dynamic? [0]:s; [1]:d;
cycle | [27, 12] | 15 | 0 | Number of cycles - 1

### LOOP

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [53, 50] | 4 | 8 | Instruction code for LOOP
extra | [53, 52] | 1 | 0 | How many following chunks?
loopid | [54, 52] | 2 | 0 | The id of the loop manager slot.
endpc | [56, 50] | 6 | 0 | The PC where loop ends.
start_sd | [62, 61] | 1 | 0 | Is the start static or dynamic? [0]:s; [1]:d;
start | [63, 57] | 6 | 0 | The start of iterator.
iter_sd | [69, 68] | 1 | 0 | Is the iteration count static or dynamic? [0]:s; [1]:d;
iter | [70, 64] | 6 | 0 | The number of iteration.
step_sd | [76, 75] | 1 | 0 | Is the step static or dynamic? [0]:s; [1]:d;
step | [77, 71] | 6 | 1 | The iteration step.

### RACCU

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [26, 23] | 4 | 10 | Instruction code for RACCU
mode | [26, 23] | 3 | 0 | RACCU mode [0]:idle; [1]:add; [2]:sub; [3]:shift_r; [4]:shift_l; [5]:mult; [6]:mult_add; [7]:mult_sub;
operand1_sd | [29, 28] | 1 | 0 | Is the first operand static or dynamic? [0]:s; [1]:d;
operand1 | [30, 23] | 7 | 0 | First operand.
operand2_sd | [37, 36] | 1 | 0 | Is the second operand static or dynamic? [0]:s; [1]:d;
operand2 | [38, 31] | 7 | 0 | Second operand.
result | [45, 41] | 4 | 0 | The RACCU register to store the result.

### BRANCH

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [26, 23] | 4 | 11 | Instruction code for BRANCH
mode | [26, 24] | 2 | 0 | The branch mode
false_pc | [28, 22] | 6 | 0 | The PC to jump to in case the condition is false.

### ROUTE

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [26, 23] | 4 | 12 | Instruction code for ROUTE
horizontal_dir | [26, 25] | 1 | 0 | The horizontal direction: West or East. [0]:w; [1]:e;
horizontal_hops. | [27, 24] | 3 | 0 | The horizontal hops - 1
vertical_dir | [30, 29] | 1 | 0 | The vertical direction: South or North. [0]:s; [1]:n;
vertical_hops | [31, 28] | 3 | 0 | The vertical hops - 1
direction | [34, 33] | 1 | 0 | The data transfer direction: Read or Write. [0]:r; [1]:w;
select_drra_row | [35, 34] | 1 | 0 | The drra row that send/recieve the data.

### SRAM

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [80, 77] | 4 | 13 | Instruction code for SRAM
rw | [80, 79] | 1 | 0 | Read or Write [0]:r; [1]:w;
init_addr | [81, 74] | 7 | 0 | Initial address
init_delay | [88, 84] | 4 | 0 | initial delay
l1_iter | [92, 85] | 7 | 0 | level-1 iteration
l1_step | [99, 91] | 8 | 1 | level-1 step
l1_delay | [107, 101] | 6 | 0 | level-1 delay
l2_iter | [113, 106] | 7 | 0 | level-2 iteration
l2_step | [120, 112] | 8 | 1 | level-2 step
l2_delay | [128, 122] | 6 | 0 | level-2 delay
init_addr_sd | [134, 133] | 1 | 0 | Is initial address static or dynamic? [0]:s; [1]:d;
l1_iter_sd | [135, 134] | 1 | 0 | Is level-1 iteration static or dynamic? [0]:s; [1]:d;
l2_iter_sd | [136, 135] | 1 | 0 | Is level-2 iteration static or dynamic? [0]:s; [1]:d;
init_delay_sd | [137, 136] | 1 | 0 | Is initial delay static or dynamic? [0]:s; [1]:d;
l1_delay_sd | [138, 137] | 1 | 0 | Is level-1 delay static or dynamic? [0]:s; [1]:d;
l2_delay_sd | [139, 138] | 1 | 0 | Is level-2 delay static or dynamic? [0]:s; [1]:d;
l1_step_sd | [140, 139] | 1 | 0 | Is level-1 step static or dynamic? [0]:s; [1]:d;
l2_step_sd | [141, 140] | 1 | 0 | Is level-2 step static or dynamic? [0]:s; [1]:d;
hops | [142, 138] | 4 | 0 | Number of hops to reach the DiMArch cell - 1