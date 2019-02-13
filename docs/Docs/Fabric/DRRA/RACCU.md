# Run-time Address Constraint Computing Unit

## Function
Run-time Address Constraint Computing Unit (RACCU) is used for computing address
constraint for Address Generation Unit (AGU). AGU can deal with maximum 2-level
affine address function. Affine address function is a function that can be expressed
by equation below:

$$
\begin{align}
y = ax+b
\end{align} 
$$

where ```a``` and ```b``` are constraints.

2-level affine address function can then be expressed as:

$$
\begin{align}
y = c(ax+b)+d
\end{align} 
$$

where ```a``` ,```b```, ```c``` and ```d``` are constraints.

Address constraint can be immediate value or a number generated at run-time according
to a given function. The resources which deal with the constraint generation is
the RACCU.

## Specification

!!! info
    Old RACCU implementation.

RACCU is a unit inside Sequencer.

It has a register file of default depth ```N=8```. Contraints and temporary
variables will be stored inside this register file. The data register is always
exposed to Sequencer to read. Another register file in RACCU is
used to manage the loops. The depth depends on maximum nested loop RACCU can handle,
by default it's ```4```. Each entry in loop management register file has 3 fields:

1. Loop id
2. Loop counter
3. Loop end flag

The loop id will be identify the entry location in the loop management register
file. Loop counter will be initialized by the first LOOP_HEADER instruction and
be changed periodically by LOOP_TAIL instruction. The comparison between the loop
conter and loop bound is carried out by LOOP_HEADER instruction. If they are equal,
loop end flag will be set to ```true``` and exits the loop.

RACCU has a computation unit which is similar to a mini-DPU. It has 5 working modes.
All of them are binary operations. They are:

- (0) RACCU_MODE_IDLE
- (1) RACCU_MODE_LOOP_H
- (2) RACCU_MODE_LOOP_T
- (3) RACCU_MODE_ADD
- (4) RACCU_MODE_SUB
- (5) RACCU_MODE_SHIFT_L
- (6) RACCU_MODE_SHIFT_R
- (7) RACCU_MODE_ADD_WITH_LOOP_INDEX

Operands of each mode can be either immediate value from instruction or data from
RACCU register whoes address is specified by the instruction. A bit is used to
distinguish the operand origin.

!!! info
    New RACCU implementation.

RACCU is a unit inside Sequencer.

It has a register file of default depth ```N=8```. Contraints, temporary
variables and loop iterators will be stored inside this register file.
The data register is always exposed to Sequencer to read. Loop iterator will be
assigned from the beginning of the register file according to the order of loop
while RACCU variables will be assigned from the end of register file. Once the
loop iterators or the RACCU variables are not needed, they can be freed by moving
the stack/heap pointer 1 position back. The assignment of register location is
managed by the Vesyla compiler.

RACCU has a computation unit which is similar to a mini-DPU. It has 8 working modes.
All of them are binary operations. They are:

- (0) RACCU_MODE_IDLE
- (1) RACCU_MODE_LOOP_H
- (2) RACCU_MODE_LOOP_T
- (3) RACCU_MODE_ADD
- (4) RACCU_MODE_SUB
- (5) RACCU_MODE_SHIFT_L
- (6) RACCU_MODE_SHIFT_R
- (7) RACCU_MODE_LOG2

Operands of each mode can be either immediate value from instruction or data from
RACCU register whoes address is specified by the instruction. A bit is used to
distinguish the operand origin.

## Related Instructions

### RACCU instruction

### LOOP_HEADER instruction

### LOOP_TAIL instruction

## Interface
!!! info
    Old RACCU implementation.
    
Signal               | I/O      | Type                     | Description
---------------------|----------|--------------------------|-------------------------
clk                  | in       | std_logic                | Clock
rst                  | in       | std_logic                | Reset, active low
op1_sd               | in       | std_logic                | Type of operand 1, 0-immediate, 1-reference
op1                  | in       | std_logic_vector 8bits   | Operand 1 value / Operand 1 address
op2_sd               | in       | std_logic                | Type of operand 2, 0-immediate, 1-reference
op2                  | in       | std_logic_vector 8bits   | Operand 2 value / Operand 2 address
cfg_mode             | in       | std_logic_vector 3bits   | Mode of RACCU computation unit
result_addr          | in       | std_logic_vector 3bits   | Result address in data_reg
data_reg             | out      | raccu_reg_out_ty         | Data register output
loop_reg             | out      | raccu_loop_array_ty      | Loop register output

!!! info
    New RACCU implementation.

Signal               | I/O      | Type                     | Description
---------------------|----------|--------------------------|-------------------------
clk                  | in       | std_logic                | Clock
rst                  | in       | std_logic                | Reset, active low
op1_sd               | in       | std_logic                | Type of operand 1, 0-immediate, 1-reference
op1                  | in       | std_logic_vector 8bits   | Operand 1 value / Operand 1 address
op2_sd               | in       | std_logic                | Type of operand 2, 0-immediate, 1-reference
op2                  | in       | std_logic_vector 8bits   | Operand 2 value / Operand 2 address
cfg_mode             | in       | std_logic_vector 3bits   | Mode of RACCU computation unit
result_addr          | in       | std_logic_vector 3bits   | Result address in data_reg
data_reg             | out      | raccu_reg_out_ty         | Data register output
