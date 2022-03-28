
!!! Note
    Instruction fields marked by bold font are controllable and observable. Users can modify these fields in Manas input file.

### HALT

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [26, 23] | 4 | 0 | Instruction code for HALT

### REFI

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [80, 77] | 4 | 1 | Instruction code for REFI
**port_no** | [76, 75] | 2 | 0 | Selects one of the RFile port. [0]:w0; [1]:w1; [2]:r0; [3]:r1;
**extra** | [74, 73] | 2 | 0 | How many following chunks?
**init_addr_sd** | [72, 72] | 1 | 0 | Is init_addr static or dymamic? [0]:s; [1]:d;
**init_addr** | [71, 66] | 6 | 0 | Initial address.
**l1_iter** | [65, 60] | 6 | 0 | Level-1 iteration - 1.
**init_delay** | [59, 54] | 6 | 0 | Initial delay.
**l1_iter_sd** | [53, 53] | 1 | 0 | Is level-1 iteration static or dymamic? [0]:s; [1]:d;
**init_delay_sd** | [52, 52] | 1 | 0 | Is initial delay static or dynamic? [0]:s; [1]:d;
unused_0 | [51, 50] | 2 | 2 | Deprecated.
**l1_step_sd** | [49, 49] | 1 | 0 | Is level-1 step static or dynamic? [0]:s; [1]:d;
**l1_step** | [48, 43] | 6 | 1 | Level-1 step
**l1_step_sign** | [42, 42] | 1 | 0 | The sign of level-1 step. [0]:+; [1]:-;
**l1_delay_sd** | [41, 41] | 1 | 0 | Is the level-1 delay static or dynamic? [0]:s; [1]:d;
**l1_delay** | [40, 37] | 4 | 0 | The level-1 delay, middle delay
**l2_iter_sd** | [36, 36] | 1 | 0 | Is level-2 iteration static or dymamic? [0]:s; [1]:d;
**l2_iter** | [35, 31] | 5 | 0 | The level-2 iteration - 1.
**l2_step** | [30, 27] | 4 | 1 | The level-2 step.
unused_1 | [26, 23] | 4 | 3 | Deprecated.
**l2_delay_sd** | [22, 22] | 1 | 0 | Is the level-2 delay static or dynamic? [0]:s; [1]:d;
**l2_delay** | [21, 16] | 6 | 0 | The level-2 delay, repetition delay.
unused_2 | [15, 10] | 6 | 0 | Deprecated.
**l1_delay_ext** | [9, 8] | 2 | 0 | The extened bits near MSB of l1_delay.
**l2_iter_ext** | [7, 7] | 1 | 0 | The extened bits near MSB of l2_iter.
**l2_step_ext** | [6, 5] | 2 | 0 | The extened bits near MSB of l2_step.
unused_3 | [4, 2] | 3 | 0 | Deprecated.
**dimarch** | [1, 1] | 1 | 0 | Is reading/writing from/to DiMArch? [0]:n; [1]:y;
**compress** | [0, 0] | 1 | 0 | Is the data compressed? [0]:n; [1]:y;

### DPU

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [26, 23] | 4 | 4 | Instruction code for DPU
**mode** | [22, 18] | 5 | 0 | The DPU mode. [0]:idle; [1]:add; [2]:sum_acc; [3]:add_const; [4]:subt; [5]:subt_abs; [6]:mode_6; [7]:mult; [8]:mult_add; [9]:mult_const; [10]:mac; [11]:ld_ir; [12]:axpy; [13]:max_min_acc; [14]:max_min_const; [15]:mode_15; [16]:max_min; [17]:shift_l; [18]:shift_r; [19]:sigm; [20]:tanhyp; [21]:expon; [22]:lk_relu; [23]:relu; [24]:div; [25]:acc_softmax; [26]:div_softmax; [28]:ld_acc; [28]:scale_dw; [29]:scale_up; [30]:mac_inter, [31]:mode_31
**control** | [17, 16] | 2 | 2 | The controll mode: saturation and operator type. [0]:nosat_int; [1]:nosat_fx; [2]:sat_int; [3]:sat_fx;
unused_0 | [15, 10] | 6 | 2 | Deprecated.
**acc_clear** | [9, 2] | 8 | 0 | The accumulator clear signal will be triggered if the accumulation reaches this number. It also serves as immediate value for some DPU mode.
**io_change** | [1, 0] | 2 | 0 | The IO mode: negate input and absolute output. [0]:no_change; [1]:negate_in0; [2]:negate_in1; [3]:abs_out;

### SWB

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [26, 23] | 4 | 5 | Instruction code for SWB
unused0 | [22, 22] | 1 | 1 | Deprecated.
**src_row** | [21, 21] | 1 | 0 | Source row.
**src_block** | [20, 20] | 1 | 0 | Source block, RF or DPU. [0]:rf; [1]:dpu;
**src_port** | [19, 19] | 1 | 0 | source port.
**hb_index** | [18, 16] | 3 | 0 | Index of horizontal bus. This is the column difference of the src and dest cell shifting by 2. For example if the path is from [0,0] to [1,2], the column difference is -2, so the hb_index = -2+2=0.
**send_to_other_row** | [15, 15] | 1 | 0 | Flag of whether src and dest row are equal. [0]:y; [1]:n;
**v_index** | [14, 12] | 3 | 0 | Index of vertical bus. This is the dest port. If destination is RF, the v_index is the port number, if the dest is DPU, the v_index is port number + 2.

### JUMP

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [26, 23] | 4 | 6 | Instruction code for JUMP
**pc** | [22, 17] | 6 | 0 | The PC to jump to

### WAIT

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [26, 23] | 4 | 7 | Instruction code for WAIT
**cycle_sd** | [22, 22] | 1 | 0 | Is the cycle static or dynamic? [0]:s; [1]:d;
**cycle** | [21, 7] | 15 | 0 | Number of cycles - 1

### LOOP

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [53, 50] | 4 | 8 | Instruction code for LOOP
**extra** | [49, 49] | 1 | 0 | How many following chunks?
**loopid** | [48, 47] | 2 | 0 | The id of the loop manager slot.
**endpc** | [46, 41] | 6 | 0 | The PC where loop ends.
**start_sd** | [40, 40] | 1 | 0 | Is the start static or dynamic? [0]:s; [1]:d;
**start** | [39, 34] | 6 | 0 | The start of iterator.
**iter_sd** | [33, 33] | 1 | 0 | Is the iteration count static or dynamic? [0]:s; [1]:d;
**iter** | [32, 27] | 6 | 0 | The number of iteration.
**step_sd** | [26, 26] | 1 | 0 | Is the step static or dynamic? [0]:s; [1]:d;
**step** | [25, 20] | 6 | 1 | The iteration step.
link | [19, 16] | 4 | 0 | The loops that have the same endpc will be linked together. This field is 1-hot encoded.

### RACCU

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [26, 23] | 4 | 10 | Instruction code for RACCU
**mode** | [22, 20] | 3 | 0 | RACCU mode [0]:idle; [1]:add; [2]:sub; [3]:shift_r; [4]:shift_l; [5]:mult; [6]:mult_add; [7]:mult_sub;
**operand1_sd** | [19, 19] | 1 | 0 | Is the first operand static or dynamic? [0]:s; [1]:d;
**operand1** | [18, 12] | 7 | 0 | First operand.
**operand2_sd** | [11, 11] | 1 | 0 | Is the second operand static or dynamic? [0]:s; [1]:d;
**operand2** | [10, 4] | 7 | 0 | Second operand.
**result** | [3, 0] | 4 | 0 | The RACCU register to store the result.

### BRANCH

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [26, 23] | 4 | 11 | Instruction code for BRANCH
**mode** | [22, 21] | 2 | 0 | The branch mode
**false_pc** | [20, 15] | 6 | 0 | The PC to jump to in case the condition is false.

### ROUTE

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [26, 23] | 4 | 12 | Instruction code for ROUTE
**horizontal_dir** | [22, 22] | 1 | 0 | The horizontal direction: West or East. [0]:w; [1]:e;
**horizontal_hops** | [21, 19] | 3 | 0 | The horizontal hops.
**vertical_dir** | [18, 18] | 1 | 0 | The vertical direction: South or North. [0]:s; [1]:n;
**vertical_hops** | [17, 15] | 3 | 0 | The vertical hops.
**direction** | [14, 14] | 1 | 0 | The data transfer direction: Read or Write. [0]:r; [1]:w;
**select_drra_row** | [13, 13] | 1 | 0 | The drra row that send/recieve the data.

### SRAM

Field | Position | Width | Default Value | Description
------|----------|-------|---------------|-------------------------
instr_code | [80, 77] | 4 | 13 | Instruction code for SRAM
**rw** | [76, 76] | 1 | 0 | Read or Write [0]:r; [1]:w;
**init_addr** | [75, 69] | 7 | 0 | Initial address
**init_delay** | [68, 65] | 4 | 0 | initial delay
**l1_iter** | [64, 58] | 7 | 0 | level-1 iteration - 1.
**l1_step** | [57, 50] | 8 | 1 | level-1 step
**l1_delay** | [49, 44] | 6 | 0 | level-1 delay
**l2_iter** | [43, 37] | 7 | 0 | level-2 iteration - 1.
**l2_step** | [36, 29] | 8 | 1 | level-2 step
**l2_delay** | [28, 23] | 6 | 0 | level-2 delay
**init_addr_sd** | [22, 22] | 1 | 0 | Is initial address static or dynamic? [0]:s; [1]:d;
**l1_iter_sd** | [21, 21] | 1 | 0 | Is level-1 iteration static or dynamic? [0]:s; [1]:d;
**l2_iter_sd** | [20, 20] | 1 | 0 | Is level-2 iteration static or dynamic? [0]:s; [1]:d;
**init_delay_sd** | [19, 19] | 1 | 0 | Is initial delay static or dynamic? [0]:s; [1]:d;
**l1_delay_sd** | [18, 18] | 1 | 0 | Is level-1 delay static or dynamic? [0]:s; [1]:d;
**l2_delay_sd** | [17, 17] | 1 | 0 | Is level-2 delay static or dynamic? [0]:s; [1]:d;
**l1_step_sd** | [16, 16] | 1 | 0 | Is level-1 step static or dynamic? [0]:s; [1]:d;
**l2_step_sd** | [15, 15] | 1 | 0 | Is level-2 step static or dynamic? [0]:s; [1]:d;
**hops** | [14, 11] | 4 | 0 | Number of hops to reach the DiMArch cell - 1
