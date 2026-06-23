# Read the outputs and debug a failed run

## Where the results go

Both tools write into the output directory (`out/` by default):

| Artifact | Produced by | Description |
| --- | --- | --- |
| `out/db.bin` | `sv-dse` | The DSE intermediate representation (JSON), consumed by Assembly. |
| `out/db_asm.bin` | `sv-asm` | The assembled, hardware-level design. |
| Per-stage images under `out/` | both | Visualisations, e.g. the routed layout. |
| `out/reassign-noc/layout_graph.png` | `sv-asm` | Final layout after memory synthesis, re-routing and NoC resynthesis. |
| `log_dse.log` / `log_asm.log` | tools | Debug logs. |

In the layout images, orange blocks are the DRRA cells of an AlImp, red and purple
blocks are output and input buffers, and green blocks are transporters; buffered
and registered wires are drawn as yellow and pink arrows.

## Functional verification

DSE automatically runs the GLIC simulator (`sv-sim`) to verify the design. The
simulator executes the application graph cycle-accurately using the bundled example
binaries in `examples/{name}/`.

When the application provides two memory images — an initial image and the expected
result image — the simulator compares them at the end. If they differ you will see

```
Failed to verify the simulation
```

after `Finish: ideal simulation`. If they match, that line is absent and the run
ends with `Sylva finished successfully!`. The simulation log prints a
`Triggering` / `Finishing` line per node and transporter, with `[@cycle]`
timestamps — useful for spotting where a schedule or address translation goes
wrong. See the [LeNet-5 tutorial](../Tutorials/Tutorial.md) for an annotated log.

## Common problems

| Symptom | Likely cause / fix |
| --- | --- |
| Binding/placement reports infeasibility | Global constraints too tight — relax `maxWidth`/`maxHeight`/`maxLatency`/`maxPeriod`/`maxEnergy` (see [Tune the optimisation](TuneOptimisation.md)). |
| Routing fails to connect an edge | Not enough routing room — raise `placeRelaxationFactor` / `placeReservedRoutingSize`. |
| `func` has no implementation | A node's `func` has no matching entry in `alimp_lib.json` — see [Provide an AlImp library](AlimpLibrary.md). |
| Edge rejected | Source/target ports missing or `tokenSize` mismatch — see [Describe your own application](CreateApplication.md). |
| GLIC synthesis / memory synthesis very slow | These are the heaviest constraint problems, especially for large communication patterns; reduce problem size or expect long solve times. |
| MiniZinc not found | Install [MiniZinc](https://www.minizinc.org/) and ensure it is on `PATH` (see [Installation](../Tutorials/Installation.md)). |
