

### HALT

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [26, 23] | 4 | 0 | Instruction code for HALT

### REFI

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [80, 77] | 4 | 1 | Instruction code for REFI
port_no | [80, 78] | 2 | 0 | Selects one of the RFile port. [0]:w0; [1]:w1; [2]:r0; [3]:r1;
extra | [78, 76] | 2 | 0 | How many following chunks?
init_addr_sd | [76, 75] | 1 | 0 | Is init_addr static or dymamic? [0]:s; [1]:d;
init_addr | [75, 69] | 6 | 0 | Initial address.
l1_iter | [69, 63] | 6 | 0 | Level-1 iteration - 1.
init_delay | [63, 57] | 6 | 0 | Initial delay.
l1_iter_sd | [57, 56] | 1 | 0 | Is level-1 iteration static or dymamic? [0]:s; [1]:d;
init_delay_sd | [56, 55] | 1 | 0 | Is initial delay static or dynamic? [0]:s; [1]:d;
unused_0 | [55, 53] | 2 | 2 | Deprecated.
l1_step_sd | [53, 52] | 1 | 0 | Is level-1 step static or dynamic? [0]:s; [1]:d;
l1_step | [52, 46] | 6 | 1 | Level-1 step
l1_step_sign | [46, 45] | 1 | 0 | The sign of level-1 step. [0]:+; [1]:-;
l1_delay_sd | [45, 44] | 1 | 0 | Is the level-1 delay static or dynamic? [0]:s; [1]:d;
l1_delay | [44, 40] | 4 | 0 | The level-1 delay, middle delay
l2_iter_sd | [40, 39] | 1 | 0 | Is level-2 iteration static or dymamic? [0]:s; [1]:d;
l2_iter | [39, 34] | 5 | 0 | The level-2 iteration.
l2_step | [34, 30] | 4 | 1 | The level-2 step.
unused_1 | [30, 26] | 4 | 3 | Deprecated.
l2_delay_sd | [26, 25] | 1 | 0 | Is the level-2 delay static or dynamic? [0]:s; [1]:d;
l2_delay | [25, 19] | 6 | 0 | The level-2 delay, repetition delay.
unused_2 | [19, 13] | 6 | 0 | Deprecated.
l1_delay_ext | [13, 11] | 2 | 0 | The extened bits near MSB of l1_delay.
l2_iter_ext | [11, 10] | 1 | 0 | The extened bits near MSB of l2_iter.
l2_step_ext | [10, 8] | 2 | 0 | The extened bits near MSB of l2_step.
unused_3 | [8, 5] | 3 | 0 | Deprecated.
dimarch | [5, 4] | 1 | 0 | Is reading/writing from/to DiMArch? [0]:n; [1]:y;
compress | [4, 3] | 1 | 0 | Is the data compressed? [0]:n; [1]:y;

### DPU

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [26, 23] | 4 | 4 | Instruction code for DPU
mode | [26, 21] | 5 | 0 | The DPU mode. [0]:idle; [1]:add; [2]:add_3; [3]:add_acc; [4]:add_const; [5]:subt; [6]:subt_abs; [7]:subt_acc; [8]:mult; [9]:mult_add; [10]:mult_const; [11]:mac; [12]:som_dist; [13]:mac_const; [14]:max_acc; [15]:max_const; [16]:min_acc; [17]:max; [18]:shift_l; [19]:shift_r; [20]:sigm; [21]:tanhyp; [22]:expon; [23]:lk_relu; [24]:elu; [25]:div; [26]:acc_softmax; [27]:div_softmax; [28]:ld_regs; [29]:st_regs; [30]:change_q;
control | [21, 19] | 2 | 2 | The controll mode: saturation and operator type. [0]:nosat_int; [1]:nosat_fx; [2]:sat_int; [3]:sat_fx;
unused_0 | [19, 13] | 6 | 2 | Deprecated.
acc_clear | [13, 5] | 8 | 0 | The accumulator clear signal will be triggered if the accumulation reaches this number. It also serves as immediate value for some DPU mode.
io_change | [5, 3] | 2 | 0 | The IO mode: negate input and absolute output. [0]:no_change; [1]:negate_in0; [2]:negate_in1; [3]:abs_out;

### SWB

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [26, 23] | 4 | 5 | Instruction code for SWB
unused0 | [26, 25] | 1 | 1 | Deprecated.
src_row | [25, 24] | 1 | 0 | Source row.
src_block | [24, 23] | 1 | 0 | Source block, RF or DPU. [0]:rf; [1]:dpu;
src_port | [23, 22] | 1 | 0 | source port.
hb_index | [22, 19] | 3 | 0 | Index of horizontal bus. This is the column difference of the src and dest cell shifting by 2. For example if the path is from [0,0] to [1,2], the column difference is -2, so the hb_index = -2+2=0.
send_to_other_row | [19, 18] | 1 | 0 | Flag of whether src and dest row are equal. [0]:y; [1]:n;
v_index | [18, 15] | 3 | 0 | Index of vertical bus. This is the dest port. If destination is RF, the v_index is the port number, if the dest is DPU, the v_index is port number + 2.

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
cycle | [25, 10] | 15 | 0 | Number of cycles - 1

### LOOP

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [53, 50] | 4 | 8 | Instruction code for LOOP
extra | [53, 52] | 1 | 0 | How many following chunks?
loopid | [52, 50] | 2 | 0 | The id of the loop manager slot.
endpc | [50, 44] | 6 | 0 | The PC where loop ends.
start_sd | [44, 43] | 1 | 0 | Is the start static or dynamic? [0]:s; [1]:d;
start | [43, 37] | 6 | 0 | The start of iterator.
iter_sd | [37, 36] | 1 | 0 | Is the iteration count static or dynamic? [0]:s; [1]:d;
iter | [36, 30] | 6 | 0 | The number of iteration.
step_sd | [30, 29] | 1 | 0 | Is the step static or dynamic? [0]:s; [1]:d;
step | [29, 23] | 6 | 1 | The iteration step.

### RACCU

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [26, 23] | 4 | 10 | Instruction code for RACCU
mode | [26, 23] | 3 | 0 | RACCU mode [0]:idle; [1]:add; [2]:sub; [3]:shift_r; [4]:shift_l; [5]:mult; [6]:mult_add; [7]:mult_sub;
operand1_sd | [23, 22] | 1 | 0 | Is the first operand static or dynamic? [0]:s; [1]:d;
operand1 | [22, 15] | 7 | 0 | First operand.
operand2_sd | [15, 14] | 1 | 0 | Is the second operand static or dynamic? [0]:s; [1]:d;
operand2 | [14, 7] | 7 | 0 | Second operand.
result | [7, 3] | 4 | 0 | The RACCU register to store the result.

### BRANCH

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [26, 23] | 4 | 11 | Instruction code for BRANCH
mode | [26, 24] | 2 | 0 | The branch mode
false_pc | [24, 18] | 6 | 0 | The PC to jump to in case the condition is false.

### ROUTE

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [26, 23] | 4 | 12 | Instruction code for ROUTE
horizontal_dir | [26, 25] | 1 | 0 | The horizontal direction: West or East. [0]:w; [1]:e;
horizontal_hops | [25, 22] | 3 | 0 | The horizontal hops - 1.
vertical_dir | [22, 21] | 1 | 0 | The vertical direction: South or North. [0]:s; [1]:n;
vertical_hops | [21, 18] | 3 | 0 | The vertical hops - 1
direction | [18, 17] | 1 | 0 | The data transfer direction: Read or Write. [0]:r; [1]:w;
select_drra_row | [17, 16] | 1 | 0 | The drra row that send/recieve the data.

### SRAM

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [80, 77] | 4 | 13 | Instruction code for SRAM
rw | [80, 79] | 1 | 0 | Read or Write [0]:r; [1]:w;
init_addr | [79, 72] | 7 | 0 | Initial address
init_delay | [72, 68] | 4 | 0 | initial delay
l1_iter | [68, 61] | 7 | 0 | level-1 iteration
l1_step | [61, 53] | 8 | 1 | level-1 step
l1_delay | [53, 47] | 6 | 0 | level-1 delay
l2_iter | [47, 40] | 7 | 0 | level-2 iteration
l2_step | [40, 32] | 8 | 1 | level-2 step
l2_delay | [32, 26] | 6 | 0 | level-2 delay
init_addr_sd | [26, 25] | 1 | 0 | Is initial address static or dynamic? [0]:s; [1]:d;
l1_iter_sd | [25, 24] | 1 | 0 | Is level-1 iteration static or dynamic? [0]:s; [1]:d;
l2_iter_sd | [24, 23] | 1 | 0 | Is level-2 iteration static or dynamic? [0]:s; [1]:d;
init_delay_sd | [23, 22] | 1 | 0 | Is initial delay static or dynamic? [0]:s; [1]:d;
l1_delay_sd | [22, 21] | 1 | 0 | Is level-1 delay static or dynamic? [0]:s; [1]:d;
l2_delay_sd | [21, 20] | 1 | 0 | Is level-2 delay static or dynamic? [0]:s; [1]:d;
l1_step_sd | [20, 19] | 1 | 0 | Is level-1 step static or dynamic? [0]:s; [1]:d;
l2_step_sd | [19, 18] | 1 | 0 | Is level-2 step static or dynamic? [0]:s; [1]:d;
hops | [18, 14] | 4 | 0 | Number of hops to reach the DiMArch cell - 1