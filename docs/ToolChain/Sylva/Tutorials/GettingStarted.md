# Sylva — Getting Started

This page walks you from a freshly built tool chain to a synthesised design in a
few minutes, using the small `minimum` example. For the full, annotated run on a
realistic application, see the [LeNet-5 tutorial](./Tutorial.md).

## 1. Install

Follow [Install Sylva](./Installation.md) to build the binaries into `bin/`.

## 2. Generate the input files

Sylva consumes five JSON input files (see the
[Sylva Input Format](../Reference/Input.md)). The `config` tool generates a
consistent set for any bundled example — `minimum`, `copy`, `sobel` or `lenet5`:

```bash
./bin/config --name minimum --output config
```

This writes `app_graph.json`, `alimp_lib.json`, `hyper_parameter.json`,
`global_constraint.json` and `technology_constraint.json` into `config/`.

## 3. Run Design Space Exploration

```bash
./bin/sv-dse \
  -g config/app_graph.json \
  -c config/global_constraint.json \
  -l config/alimp_lib.json \
  -p config/hyper_parameter.json \
  -t config/technology_constraint.json \
  -o out
```

DSE performs binding → placement → routing → NoC wire synthesis → GLIC synthesis,
then verifies the result with the GLIC simulator. The intermediate representation
is written to `out/db.bin`, and per-stage visualisations (e.g. the routed layout)
are written under `out/`.

A convenience script, `./run_dse.sh`, runs the command above with the default
paths.

## 4. Run Assembly

```bash
./bin/sv-asm \
  -i out/db.bin \
  -o out \
  --sylva-components ./sylva-components/
```

Assembly turns the abstract DSE result into a hardware-realisable design: memory
synthesis → re-routing → NoC resynthesis → TLB code generation → transporter code
generation → control synthesis. The final database is written to `out/db_asm.bin`.
The convenience script is `./run_asm.sh`.

## 5. What you get

- `out/db.bin` — the DSE intermediate representation.
- `out/db_asm.bin` — the assembled, hardware-level design.
- Layout images under `out/` (for example `out/reassign-noc/layout_graph.png`).

## Next steps

- [LeNet-5 tutorial](./Tutorial.md) — a complete, annotated end-to-end run.
- [Explanations](../Explanations/index.md) — how each synthesis stage works.
- [How-to guides](../HowToGuides/index.md) — task-oriented recipes.
- [Reference](../Reference/index.md) — input formats, the command-line interface
  and the hardware architecture.
