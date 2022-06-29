# Instruction Phase

Instruction phase is the critical timing point when the instruction behavior changes. Each instruction has 1 or more phases. It roughly corresponds to the FSM transition point of each level-2 controller. The following table list all the phases of each instruction on DRRA.

Instruction | Number | Phases
------------|--------|--------------------------------
HALT        | 1      | FETCH
REFI        | 4      | FETCH, ISSUE, ACTIVE, END
DPU         | 4      | FETCH, ISSUE, ACTIVE, END
SWB         | 3      | FETCH, ISSUE, END
JUMP        | 1      | FETCH
WAIT        | 2      | FETCH, END
LOOP        | 1      | FETCH
RACCU       | 2      | FETCH, ISSUE
BRANCH      | 1      | FETCH
ROUTE       | 4      | FETCH, ISSUE, ARRIVE, END
SRAM        | 5      | FETCH, ISSUE, ARRIVE, ACTIVE, END