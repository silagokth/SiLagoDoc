# Provide an AlImp library

The AlImp library (`alimp_lib.json`) tells Sylva which hardware implementations are
available for each algorithm used in the application graph, and supplies the
physical and timing information needed to explore the design space. Each library
entry maps a function name (matching a node's `func`) to one or more **AlImp
instances**; multiple instances per function are what give Design Space Exploration
something to choose between. The full schema is in the
[Sylva Input Format](../Reference/Input.md).

## What an instance contains

| Field | Meaning |
| --- | --- |
| `width`, `height` | DRRA-cell footprint of the implementation. |
| `latency` | Clock cycles to complete one execution. |
| `energy` | Estimated energy (unit left to the library). |
| `inputAddrTimePatterns` / `outputAddrTimePatterns` | Static data dependencies: for each data chunk, its `address` (chunk id), `channel` (DRRA vertical index) and `time` (cycle relative to the AlImp's start). |
| `instructionCode` | Flattened DRRA program plus metadata to configure the fabric. |
| `kernelObject` | Compiled object file implementing the static kernel run by the AlImp processor. |

## How it is used

- **Binding** chooses one instance per node by trading off area, energy and
  latency against the [global constraints](TuneOptimisation.md).
- **Placement / Routing** use `width`/`height` and the port positions.
- **GLIC and Memory Synthesis** use the input/output address-time patterns to
  schedule communication and size the buffers.
- **Control Synthesis** packages `instructionCode` and `kernelObject` into the
  AlImp firmware.

## Tips

- The library is normally produced by the Vesyla HLS tool. To experiment, copy a
  generated example library and adjust the instances; keep at least one instance
  per `func` referenced by your graph.
- Provide several instances per function (different `width`/`height`/`latency`/
  `energy`) so binding has a meaningful design space to explore.
- The address-time patterns must be consistent with the token sizes declared on
  the ports in `app_graph.json`.
