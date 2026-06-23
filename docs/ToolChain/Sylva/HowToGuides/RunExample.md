# Generate and run a bundled example

Sylva ships four example applications: `minimum`, `copy`, `sobel` and `lenet5`.

## 1. Generate the input files

```bash
./bin/config --name sobel --output config
```

This writes the five input files (`app_graph.json`, `alimp_lib.json`,
`hyper_parameter.json`, `global_constraint.json`, `technology_constraint.json`)
into `config/`. Use a different `--output` directory if you prefer.

## 2. Run Design Space Exploration

```bash
./bin/sv-dse \
  -g config/app_graph.json \
  -c config/global_constraint.json \
  -l config/alimp_lib.json \
  -p config/hyper_parameter.json \
  -t config/technology_constraint.json \
  -o out
```

or simply `./run_dse.sh`, which uses these default paths. DSE writes
`out/db.bin` and visualisations under `out/`, and runs the GLIC simulator to
verify the result.

## 3. Run Assembly

```bash
./bin/sv-asm -i out/db.bin -o out --sylva-components ./sylva-components/
```

or `./run_asm.sh`. Assembly writes the final design to `out/db_asm.bin`.

See the [LeNet-5 tutorial](../Tutorials/Tutorial.md) for an annotated walkthrough
of the output, including the simulation log.
