# ISA Specification

Instructions are 32-bit wide. The MSB indicates whether it's a control instruction or a resource instruction. [0]: control; [1]: resource; the next 3 bits represent the instruction opcode.
The rest of the bits are used to encode the instruction content. For resource instructions, another 4 bits in the instruction content are used to indicate the slot number.
The rest of the bits are used to encode the instruction content.

Note that for resource instructions, if the instruction opcode starts with "11", the instruction contains a field that needs to be replaced by scalar registers if the field is marked as "dynamic".

## ISA Format

| Parameter             | Width | Description                                                  |
| --------------------- | ----- | ------------------------------------------------------------ |
| instr_bitwidth        | 32    | Instruction bitwidth                                         |
| instr_type_bitwidth   | 1     | Instruction type bitwidth                                    |
| instr_opcode_bitwidth | 3     | Instruction opcode bitwidth                                  |
| instr_slot_bitwidth   | 4     | Instruction slot bitwidth, only used for resource components |

## Instructions For Each Component

### sequencer (controller)

#### halt [opcode=0]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| | | | | |

#### wait [opcode=1]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| mode | [28, 28] | 1 | 0 | Wait mode, 0 means wait for a number of cycles, 1 means wait for events. |
| cycle | [27, 1] | 27 | 0 | If mode is 0, it means the extra cycles to wait excluding the current cycle when this wait instruction is issued. If the mode is 1, this is the 1-hot encoding of event slots. |

#### act [opcode=2]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| ports | [28, 13] | 16 | 0 | 1-hot encoded ports that need to be activated. |
| mode | [12, 9] | 4 | 0 | Filter mode: [0]: Continues ports start from slot X; [1] All port X in each slot; [2]: the predefined 64-bit activation code in internal activation memory location X. (X is the filter parameter) |
| param | [8, 1] | 8 | 0 | The parameter for the filter mode. |

#### calc [opcode=3]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| mode | [28, 23] | 6 | 0 | Calculation mode  [0]: idle; [1]: add; [2]: sub; [3]: lls; [4]: lrs; [5]: mul; [6]: div; [7]: mod; [8]: bitand; [9]: bitor; [10]: bitinv; [11]: bitxor; [17]: eq; [18]: ne; [19]: gt; [20]: ge; [21]: lt; [22]: le; [32]: and; [33]: or; [34]: not; |
| operand1 | [22, 19] | 4 | 0 | First operand. |
| operand2_sd | [18, 18] | 1 | 0 | Is the second operand static or dynamic?  [0]: s; [1]: d; |
| operand2 | [17, 10] | 8 | 0 | Second operand. |
| result | [9, 6] | 4 | 0 | The register to store the result. It could be the scalar register or flag register, depending on the calculation mode. |

#### brn [opcode=4]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| reg | [28, 25] | 4 | 0 | The flag register |
| target_true | [24, 16] | 9 | 0 | The PC to jump to in case the condition is true. The PC is relative to the current PC. |
| target_false | [15, 7] | 9 | 0 | The PC to jump to in case the condition is false. The PC is relative to the current PC. |

### dpu (resource)

#### dpu [opcode=3]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| option | [24, 23] | 2 | 0 | Configuration option. |
| mode | [22, 18] | 5 | 0 | The DPU mode.   [0]: idle; [1]: add; [2]: sum_acc; [3]: add_const; [4]: subt; [5]: subt_abs; [6]: mode_6; [7]: mult; [8]: mult_add; [9]: mult_const; [10]: mac; [11]: ld_ir; [12]: axpy; [13]: max_min_acc; [14]: max_min_const; [15]: mode_15; [16]: max_min; [17]: shift_l; [18]: shift_r; [19]: sigm; [20]: tanhyp; [21]: expon; [22]: lk_relu; [23]: relu; [24]: div; [25]: acc_softmax; [26]: div_softmax; [27]: ld_acc; [28]: scale_dw; [29]: scale_up; [30]: mac_inter; [31]: mode_31; |
| immediate | [17, 2] | 16 | 0 | The immediate field used by some DPU modes. |

#### rep [opcode=0]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| port | [24, 23] | 2 | 0 | The port number  [0]: read narrow; [1]: read wide; [2]: write narrow; [3]: write wide; |
| level | [22, 19] | 4 | 0 | The level of the REP instruction. [0]: inner most level, [15]: outer most level. |
| iter | [18, 13] | 6 | 0 | level-1 iteration - 1. |
| step | [12, 7] | 6 | 1 | level-1 step |
| delay | [6, 1] | 6 | 0 | delay |

#### repx [opcode=1]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| port | [24, 23] | 2 | 0 | The port number  [0]: read narrow; [1]: read wide; [2]: write narrow; [3]: write wide; |
| level | [22, 19] | 4 | 0 | The level of the REP instruction. [0]: inner most level, [15]: outer most level. |
| iter | [18, 13] | 6 | 0 | level-1 iteration - 1. |
| step | [12, 7] | 6 | 1 | level-1 step |
| delay | [6, 1] | 6 | 0 | delay |

#### fsm [opcode=2]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| port | [24, 23] | 2 | 0 | The port number |
| delay_0 | [22, 16] | 7 | 0 | Delay between state 0 and 1. |
| delay_1 | [15, 9] | 7 | 0 | Delay between state 1 and 2. |
| delay_2 | [8, 2] | 7 | 0 | Delay between state 2 and 3. |

### dpu_2cycle_mac (resource)

#### dpu [opcode=3]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| option | [24, 23] | 2 | 0 | Configuration option. |
| mode | [22, 18] | 5 | 0 | The DPU mode.   [0]: idle; [1]: add; [2]: sum_acc; [3]: add_const; [4]: subt; [5]: subt_abs; [6]: mode_6; [7]: mult; [8]: mult_add; [9]: mult_const; [10]: mac; [11]: ld_ir; [12]: axpy; [13]: max_min_acc; [14]: max_min_const; [15]: mode_15; [16]: max_min; [17]: shift_l; [18]: shift_r; [19]: sigm; [20]: tanhyp; [21]: expon; [22]: lk_relu; [23]: relu; [24]: div; [25]: acc_softmax; [26]: div_softmax; [27]: ld_acc; [28]: scale_dw; [29]: scale_up; [30]: mac_inter; [31]: mode_31; |
| immediate | [17, 2] | 16 | 0 | The immediate field used by some DPU modes. |

#### rep [opcode=0]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| port | [24, 23] | 2 | 0 | The port number  [0]: read narrow; [1]: read wide; [2]: write narrow; [3]: write wide; |
| level | [22, 19] | 4 | 0 | The level of the REP instruction. [0]: inner most level, [15]: outer most level. |
| iter | [18, 13] | 6 | 0 | level-1 iteration - 1. |
| step | [12, 7] | 6 | 1 | level-1 step |
| delay | [6, 1] | 6 | 0 | delay |

#### repx [opcode=1]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| port | [24, 23] | 2 | 0 | The port number  [0]: read narrow; [1]: read wide; [2]: write narrow; [3]: write wide; |
| level | [22, 19] | 4 | 0 | The level of the REP instruction. [0]: inner most level, [15]: outer most level. |
| iter | [18, 13] | 6 | 0 | level-1 iteration - 1. |
| step | [12, 7] | 6 | 1 | level-1 step |
| delay | [6, 1] | 6 | 0 | delay |

#### fsm [opcode=2]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| port | [24, 23] | 2 | 0 | The port number |
| delay_0 | [22, 16] | 7 | 0 | Delay between state 0 and 1. |
| delay_1 | [15, 9] | 7 | 0 | Delay between state 1 and 2. |
| delay_2 | [8, 2] | 7 | 0 | Delay between state 2 and 3. |

### iosram_both (resource)

#### dsu [opcode=6]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| init_addr_sd | [24, 24] | 1 | 0 | Is initial address static or dynamic?  [0]: s; [1]: d; |
| init_addr | [23, 8] | 16 | 0 | Initial address |
| port | [7, 6] | 2 | 0 | The port number  [0]: address generation for input buffer; [1]: address generation for output buffer; [2]: if in first slot, address generation for writing to SRAM from input buffer; if in second slot, address generation for writing to SRAM (BULK); [3]: if in first slot, address generation for read from SRAM to output buffer; if in second slot, address generation for reading from SRAM (BULK); |

#### rep [opcode=0]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| port | [24, 23] | 2 | 0 | The port number  [0]: address generation for input buffer; [1]: address generation for output buffer; [2]: if in first slot, address generation for writing to SRAM from input buffer; if in second slot, address generation for writing to SRAM (BULK); [3]: if in first slot, address generation for read from SRAM to output buffer; if in second slot, address generation for reading from SRAM (BULK); |
| level | [22, 19] | 4 | 0 | The level of the REP instruction. [0]: inner most level, [15]: outer most level. |
| iter | [18, 13] | 6 | 0 | level-1 iteration - 1. |
| step | [12, 7] | 6 | 1 | level-1 step |
| delay | [6, 1] | 6 | 0 | delay |

#### repx [opcode=1]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| port | [24, 23] | 2 | 0 | The port number  [0]: address generation for input buffer; [1]: address generation for output buffer; [2]: if in first slot, address generation for writing to SRAM from input buffer; if in second slot, address generation for writing to SRAM (BULK); [3]: if in first slot, address generation for read from SRAM to output buffer; if in second slot, address generation for reading from SRAM (BULK); |
| level | [22, 19] | 4 | 0 | The level of the REP instruction. [0]: inner most level, [15]: outer most level. |
| iter | [18, 13] | 6 | 0 | level-1 iteration - 1. |
| step | [12, 7] | 6 | 1 | level-1 step |
| delay | [6, 1] | 6 | 0 | delay |

### iosram_btm (resource)

#### dsu [opcode=6]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| init_addr_sd | [24, 24] | 1 | 0 | Is initial address static or dynamic?  [0]: s; [1]: d; |
| init_addr | [23, 8] | 16 | 0 | Initial address |
| port | [7, 6] | 2 | 0 | The port number  [0]: address generation for input buffer; [1]: address generation for output buffer; [2]: if in first slot, address generation for writing to SRAM from input buffer; if in second slot, address generation for writing to SRAM (BULK); [3]: if in first slot, address generation for read from SRAM to output buffer; if in second slot, address generation for reading from SRAM (BULK); |

#### rep [opcode=0]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| port | [24, 23] | 2 | 0 | The port number  [0]: address generation for input buffer; [1]: address generation for output buffer; [2]: if in first slot, address generation for writing to SRAM from input buffer; if in second slot, address generation for writing to SRAM (BULK); [3]: if in first slot, address generation for read from SRAM to output buffer; if in second slot, address generation for reading from SRAM (BULK); |
| level | [22, 19] | 4 | 0 | The level of the REP instruction. [0]: inner most level, [15]: outer most level. |
| iter | [18, 13] | 6 | 0 | level-1 iteration - 1. |
| step | [12, 7] | 6 | 1 | level-1 step |
| delay | [6, 1] | 6 | 0 | delay |

#### repx [opcode=1]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| port | [24, 23] | 2 | 0 | The port number  [0]: address generation for input buffer; [1]: address generation for output buffer; [2]: if in first slot, address generation for writing to SRAM from input buffer; if in second slot, address generation for writing to SRAM (BULK); [3]: if in first slot, address generation for read from SRAM to output buffer; if in second slot, address generation for reading from SRAM (BULK); |
| level | [22, 19] | 4 | 0 | The level of the REP instruction. [0]: inner most level, [15]: outer most level. |
| iter | [18, 13] | 6 | 0 | level-1 iteration - 1. |
| step | [12, 7] | 6 | 1 | level-1 step |
| delay | [6, 1] | 6 | 0 | delay |

### iosram_top (resource)

#### dsu [opcode=6]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| init_addr_sd | [24, 24] | 1 | 0 | Is initial address static or dynamic?  [0]: s; [1]: d; |
| init_addr | [23, 8] | 16 | 0 | Initial address |
| port | [7, 6] | 2 | 0 | The port number  [0]: address generation for input buffer; [1]: address generation for output buffer; [2]: if in first slot, address generation for writing to SRAM from input buffer; if in second slot, address generation for writing to SRAM (BULK); [3]: if in first slot, address generation for read from SRAM to output buffer; if in second slot, address generation for reading from SRAM (BULK); |

#### rep [opcode=0]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| port | [24, 23] | 2 | 0 | The port number  [0]: address generation for input buffer; [1]: address generation for output buffer; [2]: if in first slot, address generation for writing to SRAM from input buffer; if in second slot, address generation for writing to SRAM (BULK); [3]: if in first slot, address generation for read from SRAM to output buffer; if in second slot, address generation for reading from SRAM (BULK); |
| level | [22, 19] | 4 | 0 | The level of the REP instruction. [0]: inner most level, [15]: outer most level. |
| iter | [18, 13] | 6 | 0 | level-1 iteration - 1. |
| step | [12, 7] | 6 | 1 | level-1 step |
| delay | [6, 1] | 6 | 0 | delay |

#### repx [opcode=1]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| port | [24, 23] | 2 | 0 | The port number  [0]: address generation for input buffer; [1]: address generation for output buffer; [2]: if in first slot, address generation for writing to SRAM from input buffer; if in second slot, address generation for writing to SRAM (BULK); [3]: if in first slot, address generation for read from SRAM to output buffer; if in second slot, address generation for reading from SRAM (BULK); |
| level | [22, 19] | 4 | 0 | The level of the REP instruction. [0]: inner most level, [15]: outer most level. |
| iter | [18, 13] | 6 | 0 | level-1 iteration - 1. |
| step | [12, 7] | 6 | 1 | level-1 step |
| delay | [6, 1] | 6 | 0 | delay |

### rf (resource)

#### dsu [opcode=6]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| init_addr_sd | [24, 24] | 1 | 0 | Is initial address static or dynamic?  [0]: s; [1]: d; |
| init_addr | [23, 8] | 16 | 0 | Initial address |
| port | [7, 6] | 2 | 0 | The port number  [0]: Write to RF (WORD); [1]: Read from RF (WORD); [2]: Write to RF (BULK); [3]: Read from RF (BULK); |

#### rep [opcode=0]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| port | [24, 23] | 2 | 0 | The port number  [0]: Write to RF (WORD); [1]: Read from RF (WORD); [2]: Write to RF (BULK); [3]: Read from RF (BULK); |
| level | [22, 19] | 4 | 0 | The level of the REP instruction. [0]: inner most level, [15]: outer most level. |
| iter | [18, 13] | 6 | 0 | level-1 iteration - 1. |
| step | [12, 7] | 6 | 1 | level-1 step |
| delay | [6, 1] | 6 | 0 | delay |

#### repx [opcode=1]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| port | [24, 23] | 2 | 0 | The port number  [0]: Write to RF (WORD); [1]: Read from RF (WORD); [2]: Write to RF (BULK); [3]: Read from RF (BULK); |
| level | [22, 19] | 4 | 0 | The level of the REP instruction. [0]: inner most level, [15]: outer most level. |
| iter | [18, 13] | 6 | 0 | level-1 iteration - 1. |
| step | [12, 7] | 6 | 1 | level-1 step |
| delay | [6, 1] | 6 | 0 | delay |

### swb (resource)

#### swb [opcode=4]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| option | [24, 23] | 2 | 0 | configuration option |
| channel | [22, 19] | 4 | 0 | Bus channel. Note: if the SWB is implemented by a crossbar, the channel is always equals to the target slot. |
| source | [18, 15] | 4 | 0 | Source slot. |
| target | [14, 11] | 4 | 0 | Target slot. |

#### route [opcode=5]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| option | [24, 23] | 2 | 0 | configuration option |
| sr | [22, 22] | 1 | 0 | Send or receive.  [0]: s; [1]: r; |
| source | [21, 18] | 4 | 0 | If it's on sending side, it indicates the slot number; if it's on receiving side, it's encoded direction NW/N/NE/W/C/E/SW/S/SE. (Binary encoded) |
| target | [17, 2] | 16 | 0 | If it's on sending side, it's encoded direction NW/N/NE/W/C/E/SW/S/SE; if it's on receiving side, it indicates the slot number. (1-hot encoded) |

#### rep [opcode=0]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| port | [24, 23] | 2 | 0 | The port number  [0]: read narrow; [1]: read wide; [2]: write narrow; [3]: write wide; |
| level | [22, 19] | 4 | 0 | The level of the REP instruction. [0]: inner most level, [15]: outer most level. |
| iter | [18, 13] | 6 | 0 | level-1 iteration - 1. |
| step | [12, 7] | 6 | 1 | level-1 step |
| delay | [6, 1] | 6 | 0 | delay |

#### repx [opcode=1]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| port | [24, 23] | 2 | 0 | The port number  [0]: read narrow; [1]: read wide; [2]: write narrow; [3]: write wide; |
| level | [22, 19] | 4 | 0 | The level of the REP instruction. [0]: inner most level, [15]: outer most level. |
| iter | [18, 13] | 6 | 0 | level-1 iteration - 1. |
| step | [12, 7] | 6 | 1 | level-1 step |
| delay | [6, 1] | 6 | 0 | delay |

#### fsm [opcode=2]

| Field | Position | Width | Default Value | Description |
| ----- | -------- | ----- | ------------- | ----------- |
| port | [24, 23] | 2 | 0 | The port number |
| delay_0 | [22, 16] | 7 | 0 | Delay between state 0 and 1. |
| delay_1 | [15, 9] | 7 | 0 | Delay between state 1 and 2. |
| delay_2 | [8, 2] | 7 | 0 | Delay between state 2 and 3. |

