# Installation and Usage

Sylva and its dependency packages have to be installed on your system to use the
tool chain. The following sections describe how to install Sylva and its
dependencies.

## Operating system

Sylva is developed and tested on **Linux (Ubuntu)** and relies on standard Unix
utilities; it may not work on non-Unix systems without modification.

- Ubuntu 22.04 / 24.04 (or a compatible Linux distribution).

## Requirements

Sylva is written in Rust and, at runtime, calls the **MiniZinc** constraint
solver. The build/run scripts also use a few standard Unix tools, Assembly's
control synthesis reads the RTL components from the `sylva-components` submodule,
and the full hardware/RTL flow uses additional tooling. The required and optional
packages are:

| Package | Tested version | Needed for |
| --- | --- | --- |
| `bash` | — | Running the `setup.sh` / `run_*.sh` scripts. |
| `coreutils` (`cp`, `mv`, `rm`, …) | — | File handling performed by the scripts. |
| `make` | — | Build steps invoked by the tool chain. |
| [Rust toolchain (rustc + Cargo)](https://www.rust-lang.org/) | 1.88+ | Building the tool chain. |
| [MiniZinc](https://www.minizinc.org/) | 2.9+ | Constraint programming during DSE and Assembly (uses the CP-SAT backend). |
| [riscv-gnu-toolchain](https://github.com/riscv-collab/riscv-gnu-toolchain) | 2026.03.13+ | Compiling processor/AlImp firmware (control synthesis). |
| [bender](https://github.com/pulp-platform/bender) | 0.30.0+ | Resolving the `sylva-components` RTL dependencies. |
| [Questa](https://www.altera.com/products/development-tools/quartus-prime/questa) (`vsim`) | 2023.4+ | (Optional) RTL-level simulation/verification of the generated hardware. |

On Debian/Ubuntu the following system packages are needed to build Sylva:

```bash
apt install -y \
  build-essential \
  pkg-config \
  libfontconfig1-dev
```

## Sylva Installation

Sylva is hosted on GitHub at
[silagokth/sylva-suite](https://github.com/silagokth/sylva-suite). It bundles the
[`sylva-components`](https://github.com/silagokth/sylva-components) RTL repository
as a git submodule, so clone it recursively:

```bash
git clone --recurse-submodules https://github.com/silagokth/sylva-suite.git
# if you already cloned without submodules:
git submodule update --init --recursive
```

To build the binaries, run the setup script in the main directory:

```bash
./setup.sh
```

The script and binaries were tested on Ubuntu 24.04.2 LTS. It compiles the
workspace with Cargo and produces the Design Space Exploration tool, the Assembly
tool, the GLIC simulator and the example file generator, placing them in a `bin/`
sub-directory:

```
bin
├── config     # generates input configuration files for the bundled examples
├── sv-asm      # Assembly
├── sv-dse      # Design Space Exploration
└── sv-sim      # GLIC functional simulator
```

## Example Installation (optional)

The bundled test cases (`minimum`, `copy`, `sobel`, `lenet5`) are compiled and
ready to use in the `examples/` directory after `setup.sh` finishes. Their input
configuration files are generated with the `config` tool — see
[Getting Started](./GettingStarted.md).

## Verifying the installation

```bash
./bin/sv-dse --version
./bin/sv-asm --version
minizinc --version
```

If any binary is missing, re-run `./setup.sh` and check the Cargo build output.
