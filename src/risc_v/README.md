# `risc_v/` – RISC-V Processor Model

This package implements shared components of a 32-bit RISC-V (RV32I) processor model:

1. **Configuration** (`riscv_config.py`) – shared constants, enums,
   control-signal dataclasses, and the `Instruction` bit-field decoder.
2. **Reusable modules** (`modules/`) – combinational datapath blocks (ALU, ImmGen,
   shifter, PC, etc.).
3. **Memory interfaces** (`mem/`) – Register File, data memory read/write ports,
   and pipeline-specific memory wrappers.

Processor microarchitectures (`models/single_cycle/`, `models/pipeline/`) are
implemented in `models/`, both conforming to the `ICpuSystem` interface defined in
`base/`.

The `base/` sub-package defines the abstract `ICpuSystem` contract that decouples
verification (in `tests/`) from the concrete microarchitecture.

## Contents

| Item | Document | Description |
| --- | --- | --- |
| `riscv_config.py` | — | Global constants (`XLEN`, `PC_START_ADDR`, memory widths) and enums/control dataclasses. |
| `base/` | [README](base/README.md) | `ICpuSystem` abstract interface. |
| `mem/` | — | `RegFile`, `dmem_rd_port`, `dmem_wr_port`, and memory wrappers. |
| `modules/` | [README](modules/README.md) | ALU, Shifter, BranchComparator, ImmGen, PC. |

## `riscv_config.py` – Shared Configuration

Central definitions used across all cores:

- **`XLEN = 32`**, `PC_START_ADDR = 0`, `IMEM_ADDR_BYTE_WIDTH` /
  `DMEM_ADDR_BYTE_WIDTH = 14`, derived `BYTE_ADDR_WIDTH` and `DATA_BYTE_NUM`.
- **Enums** (control/signal encoding):
  - `Alu_sel_t` – ADD, SUB, AND, OR, XOR, SLT, SLTU, LUI, JALR.
  - `Shift_sel_t` – SLL, SRL, SRA, ANY.
  - `Instr_type_t` – TYPE_I/S/B/U/J and TYPE_ANY (immediate format).
  - `WB_sel_t` – PC4_OUT, ALU_OUT, SHIFTER_OUT, DMEM_OUT (write-back mux).
  - `DMem_sel` – load/store opcodes (LB/LH/LW/LBU/LHU/SB/SH/SW) with factory
    methods `from_load_funct3`, `from_store_funct3`, `from_int`, and helpers
    `funct3()`, `is_write()`, `to_int()`.
- **`Id_controls_out`** – dataclass holding every decode-stage control signal
  (`reg_wr`, `dmem_sel`, `a_sel`, `b_sel`, `sh_sel`, `br_un`, `pc_sel`,
  `alu_sel`, `wb_sel`, `imm_type`, `illegal`, `jf_exe`, `alushift_sel`).
- **`Instruction`** – decodes a raw 32-bit word into `opcode`, `rd`, `rs1`,
  `rs2`, `funct3`, `funct7`, `funct7_onebit`, `shamt`. Validates the 32-bit
  range. Provides `__repr__`/`__str__`.

## Canonical Cores

The canonical, microarchitecture-correct cores used by the test suite are
implemented under `models/`:
- Single-Cycle Core: `models/single_cycle/cpu_system.py`
- 5-Stage Pipelined Core: `models/pipeline/cpu_system.py`
