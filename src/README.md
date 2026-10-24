# `src/` – Source Code

This directory is the Python source root of the project. It follows a `src`
layout: all importable packages live directly under `src/` and the package
discovery in `pyproject.toml` is rooted here.

It contains three top-level packages:

- **`models/`** – the CPU microarchitectures (`single_cycle` and `pipeline`).
- **`risc_v/`** – the RISC-V processor model (shared configuration, reusable
  datapath modules, and memory interfaces).
- **`sim_base/`** – a small, processor-agnostic hardware simulation framework
  (clocks, registers, memories) on which `risc_v` and `models` are built.

A standalone entry point, `main.py`, serves as an example simulation driver.

For the package-level overview see the [root README](../README.md).

## Sub-package Index

| Path | Document | What it contains |
| --- | --- | --- |
| `main.py` | — | Manual simulation driver / usage example. |
| `models/pipeline/` | [README](models/pipeline/README.md) | 5-stage pipelined core. |
| `models/pipeline/stages/` | [README](models/pipeline/stages/README.md) | IF/ID/EX/MEM/WB stage logic. |
| `models/single_cycle/` | [README](models/single_cycle/README.md) | Single-cycle core. |
| `risc_v/` | [README](risc_v/README.md) | Configuration + datapath modules. |
| `risc_v/base/` | [README](risc_v/base/README.md) | `ICpuSystem` abstract interface. |
| `risc_v/mem/` | — | Register File and memory ports. |
| `risc_v/modules/` | [README](risc_v/modules/README.md) | Datapath building blocks. |
| `sim_base/` | [README](sim_base/README.md) | Simulation framework. |
| `sim_base/core/` | [README](sim_base/core/README.md) | Clock / trigger / combinational interfaces. |
| `sim_base/mem/` | [README](sim_base/mem/README.md) | Register and memory primitives. |

## Architectural Layers (bottom-up)

```text
sim_base/        low-level simulation primitives (Clock, Register, Memories)
   ▲
risc_v/modules/  datapath blocks (ALU, Shifter, BranchUnit, ImmGen, Decode, PC, mem)
   ▲
models/{single_cycle,pipeline}/   processor cores (implement ICpuSystem)
   ▲
tests/           verification, sharing the ICpuSystem contract
```

## Conventions

- All datapath modules expose **static methods** (`Alu.execute`, `ImmGen.generate`,
  `BranchUnit.compare`, `Shifter.shift`, `Instruction_Decoder.decode`, …).
- Stateful, sequential elements (`Register`, `BlockMem`, `RegFile`, pipeline
  buffers) separate a combinational `set()`/`read()` API from an `update()`
  method that commits the next value on a clock tick.
- Instruction values are 32-bit (`conf.XLEN = 32`); registers and memory cells
  are masked to `XLEN` bits.
