# Command-line Interface

The tool chain builds four binaries into `bin/`. This page documents their
arguments.

## `sv-dse` — Design Space Exploration

Runs binding, placement, routing, NoC wire synthesis and GLIC synthesis, then
verifies the result with the GLIC simulator. Produces `db.bin`.

```text
Usage: sv-dse [OPTIONS] --graph <GRAPH> --constraint <CONSTRAINT_FILE>
              --library <ALIMP_LIB> --parameter <HYPER_PARAMETER>
              --technology <TECHNOLOGY_CONSTRAINT> --output <OUTPUT>

Options:
      --cpu <CPU_LIMIT>                     Set CPU limit
      --memory <MEMORY_LIMIT>               Set memory limit in GB
  -g, --graph <GRAPH>                       SDF graph file (app_graph.json)
  -c, --constraint <CONSTRAINT_FILE>        global constraint file
  -l, --library <ALIMP_LIB>                 alimp library file
  -p, --parameter <HYPER_PARAMETER>         hyper parameter file
  -t, --technology <TECHNOLOGY_CONSTRAINT>  technology constraint file
  -o, --output <OUTPUT>                     output directory
  -h, --help                                Print help
  -V, --version                             Print version
```

## `sv-asm` — Assembly

Consumes the DSE intermediate representation and runs memory synthesis,
re-routing, NoC resynthesis, TLB code generation, transporter code generation and
control synthesis. Produces `db_asm.bin`. Requires the path to the
`sylva-components` submodule (used during control synthesis).

```text
Usage: sv-asm [OPTIONS] --intermediate-representation <IR_OBJECT>
              --sylva-components <SYLVA_COMPONENTS> --binary <OUTPUT>

Options:
      --cpu <CPU_LIMIT>                          Set CPU limit
      --memory <MEMORY_LIMIT>                    Set memory limit in GB
  -i, --intermediate-representation <IR_OBJECT>  Input Intermediate Representation Object (db.bin)
      --sylva-components <SYLVA_COMPONENTS>      Path to the Sylva components
  -o, --binary <OUTPUT>                          output directory
  -h, --help                                     Print help
  -V, --version                                  Print version
```

## `sv-sim` — GLIC functional simulator

Cycle-accurately simulates a design from the generated scheduling/communication
files in a directory. It is invoked automatically by `sv-dse`, but can also be run
on its own.

```text
Usage: sv-sim --dir <DIR>

Options:
      --dir <DIR>  Directory containing the simulation files
  -h, --help       Print help
  -V, --version    Print version
```

## `config` — example generator

Generates the five input files for a bundled example application.

```text
Usage: config --name <NAME> --output <DIR>

Options:
  -n, --name <NAME>   name of the generating example (minimum, copy, sobel, lenet5)
  -o, --output <DIR>  output directory
  -h, --help          Print help
  -V, --version       Print version
```

## Convenience scripts

| Script | Equivalent to |
| --- | --- |
| `./setup.sh` | Build all binaries into `bin/` and stage the example binaries. |
| `./run_dse.sh` | `sv-dse` with the default `config/` inputs and `out/` output. |
| `./run_asm.sh` | `sv-asm -i out/db.bin -o out --sylva-components ./sylva-components/`. |
