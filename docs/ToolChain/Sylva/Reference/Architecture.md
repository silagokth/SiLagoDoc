# Hardware Reference

Reference tables for the hardware that Sylva targets. For the narrative
explanation, see [Hardware Architecture](../Explanations/Architecture.md); the RTL
lives in [`sylva-components`](https://github.com/silagokth/sylva-components).

## Transporter instruction set

The transporter is a non-pipelined processor with a 16-entry, 16-bit register file
(R0 hardwired to 0 → 15 usable), 32-bit instructions and an addition-only ALU.

### Instruction formats

| Type | Bit layout (32 bits) |
| --- | --- |
| 0R | `OPCODE(4)` · `UNUSED(12)` · `IMMEDIATE(16)` |
| 1R | `OPCODE(4)` · `R0(4)` · `UNUSED(8)` · `IMMEDIATE(16)` |
| 2R | `OPCODE(4)` · `R0(4)` · `R1(4)` · `UNUSED(4)` · `IMMEDIATE(16)` |
| 3R | `OPCODE(4)` · `R0(4)` · `R1(4)` · `R2(4)` · `IMMEDIATE(16)` |

### Instructions

| Instruction | Opcode | Type | Function |
| --- | --- | --- | --- |
| `NOP`  | `0000` | 0R | Wait `IMMEDIATE` cycles. If `IMMEDIATE == 0`, wait indefinitely. |
| `LDI`  | `0010` | 1R | Load `IMMEDIATE` into `R0`. |
| `MOV`  | `1000` | 2R | Move data from `OB[R1 + IMMEDIATE]` to the next `IB[R0 + IMMEDIATE]`. |
| `MOVC` | `1001` | 3R | Repeat `MOV` for `n ∈ {0,…,IMMEDIATE−1}` with stride `R2`; `IMMEDIATE` must be > 0. |

Immediates are signed and sign-extended to the datapath width, except the `NOP`
immediate, which is unsigned. A rising edge of `start` resets the program counter
to 0; a program ends with `NOP 0`, stalling the transporter until the next start.
The RTL additionally decodes `NOPR (0001)`; `BRN`/`CAL`/`CALI` opcodes are present
but unimplemented.

## Global and AlImp region address map

The system address space (host view). Address bits `[27:18]` of the AlImp region
select an AlImp, supporting up to ~1024 of them.

| Region | Base | Size | End | Description |
| --- | --- | --- | --- | --- |
| Host instruction memory | `0x0000_0000` | `0x0001_0000` | `0x0000_FFFF` | Host CPU instruction space. |
| Host data memory | `0x0001_0000` | `0x0002_0000` | `0x0002_FFFF` | Host CPU data space. |
| Peripheral region | `0x0008_0000` | `0x0001_0000` | `0x0008_FFFF` | Memory-mapped IO (incl. global timer). |
| AlImp region | `0x4000_0000` | `0x1000_0000` | `0x4FFF_FFFF` | All AlImps (bits [27:18] select one). |
| ↳ CPU instruction memory | `0x4000_0000` | `0x0001_0000` | `0x4000_FFFF` | |
| ↳ CPU data memory | `0x4001_0000` | `0x0001_0000` | `0x4001_FFFF` | |
| ↳ CPU communication memory | `0x4002_0000` | `0x0001_0000` | `0x4002_FFFF` | Mailbox. |
| ↳ CPU reset control | `0x4003_0000` | `0x0001_0000` | `0x4003_FFFF` | |
| External interface | `0x8000_0000` | `0x8000_0000` | `0xFFFF_FFFF` | External memory. |

## AlImp CPU internal address map

The local address space seen by an AlImp's processor.

| Region | Base | Size | End | Type | Description |
| --- | --- | --- | --- | --- | --- |
| CPU instruction memory | `0x0000_0000` | `0x0001_0000` | `0x0000_FFFF` | u32 | Instruction memory. |
| CPU data memory | `0x0001_0000` | `0x0001_0000` | `0x0001_FFFF` | u32 | Data memory. |
| CPU communication memory | `0x0002_0000` | `0x0001_0000` | `0x0002_FFFF` | u32 | Shared mailbox memory. |
| Synchroniser — reference time | `0x0010_0000` | `0x0000_0008` | `0x0010_0008` | u64 | Reference start cycle. |
| Synchroniser — transporter offsets | `0x0010_0100` | `0x0000_0040` | `0x0010_013F` | u32 | Per-transporter relative offsets. |
| Synchroniser — read-to-start | `0x0010_0200` | `0x0000_0004` | `0x0010_0204` | u1 | Blocking read that stalls to the reference cycle. |
| DRRA — instruction loader config | `0x1000_0000` | `0x0000_0004` | `0x1000_0004` | u32 | |
| DRRA — loader start address | `0x1000_0004` | `0x0000_0004` | `0x1000_0008` | u32 | |
| DRRA — loader chunk count | `0x1000_0008` | `0x0000_0004` | `0x1000_000C` | u32 | |
| DRRA — loader data | `0x1000_000C` | `0x0000_0004` | `0x1000_0010` | u32 | |
| DRRA — start signal | `0x1000_0010` | `0x0000_0004` | `0x1000_0014` | u1 | |
| DRRA — completion status | `0x1000_0014` | `0x0000_0004` | `0x1000_0018` | u1 | |
| IB translation loader | `0x1010_0000` | `0x000F_FFFF` | `0x101F_FFFF` | u32 | IB TLB programming. |
| OB translation loader | `0x1020_0000` | `0x000F_FFFF` | `0x102F_FFFF` | u32 | OB TLB programming. |
| Transporter loader | `0x1030_0000` | `0x000F_FFFF` | `0x103F_FFFF` | u32 | Transporter program. |
| Transporter status | `0x1040_0000` | `0x000F_FFFF` | `0x104F_FFFF` | u1 | Combined transporter busy. |
| Application data memory | `0x1080_0000` | `0x000F_FFFF` | `0x108F_FFFF` | u32 | Application data read/write. |

## Host ↔ AlImp mailbox

A memory-mapped structure in each AlImp's communication memory used for two-way
coordination. Separate valid bits prevent races.

| Field | Type | Description |
| --- | --- | --- |
| `host_valid` | u32 | Valid bit set by the host processor. |
| `cpu_valid` | u32 | Valid bit set by the AlImp processor. |
| `status` | u32 | Shared status register between host and AlImp. |
| `cpu_time_lo` | u32 | Lower 32 bits of the scheduled CPU execution (reference) time. |
| `cpu_time_hi` | u32 | Upper 32 bits of the scheduled CPU execution (reference) time. |
| `tp_time[i]` | i32 | Relative time offset for transporter `i` (`i ∈ [0, MAXIMUM_TRANSPORTERS−1]`). |

## AXI interfaces

Both the AlImp-side AXI-Lite slave and the host-side AXI-Lite master implement the
32-bit AXI-Lite protocol with a single outstanding transaction at a time. Input
signals are registered to ease timing closure, adding at least one cycle of latency
per transaction.
