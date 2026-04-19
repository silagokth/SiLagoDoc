# Installation and Usage

Sylva and its dependency packages have to be installed on your system to use the tool chain. The following sections describe how to install Sylva and its dependency packages.

## Requirements

Sylva code is written and managed by Rust programming language. During runtime, it uses a dependency tool for constraint programming called **"minizinc"**. The following packages are required to install Sylva tool chain:

1. [Rust compiler with Cargo](https://www.rust-lang.org/) (tested rustc version 1.88.0) 
2. [MiniZinc tool](https://www.minizinc.org/) (tested minizinc version 2.9.3)


## Sylva Installation

Sylva is currently hosted on GitHub at [silagokth/sylva-suite](https://github.com/silagokth/sylva-suite). The tool chain is mainly written in Rust. To build binaries for the tool chain, it can be simply done by executing the below script in the main directory.

```bash
./setup.sh
```

The script and binaries were tested on Ubuntu 24.04.2 LTS. The script compiles the code using Cargo and produces the Sylva Design Space Exploration tool, the Sylva Assembly tool, the Design Space Exploration Simulator, and a file generator for creating test examples. It will also create a subfolder called "bin" in the main directory to put the following binaries:

```
bin
├── config
├── sv-asm
├── sv-dse
└── sv-sim
```

## Example Installation (Optional)

Provided testcases are included in the Git repository. Now, they are already compiled and ready-to-go in ```examples\``` directory after you install Sylva Tool chain.
