# DRRA Components CMake Build System Reference

This reference document describes the CMake build system used in the [DRRA Components project](https://github.com/silagokth/drra-components).

## Build

### Overview

The DRRA Components project uses CMake as its primary build system.
The tasks of the build system include:

- At CMake configuration time
  - Copying the architecture description file (`arch.json`) and instruction set
    description file (`isa.json`) of each component to the build directory
  - Copying the technology description file (e.g., `tech:gf22.json`)
    of each component to the build directory
  - Copying `rtl` folders of each component to the build directory
  - Copying `Bender.yml` of each component to the build directory
  - Copying fabric source rtl (`lib/fabric/rtl`), Bender file
    (`lib/fabric/Bender.yml`), and testbench (`lib/tb`) to the build directory

- At build time
  - Building SST simulation models of each component
  - Building `timing_model` executables of each component
    (e.g., using Corrosion for Rust)

- Post build
  - Registering the built shared library (`libdrra.so`)
    with the SST simulation framework

### Dependencies

| Dependency                        | Purpose                  |
| --------------------------------- | ------------------------ |
| CMake (≥ 3.14)                    | Build system             |
| *IF `USE_SST=ON` (default)*       |
| C++ Compiler                      | Component compilation    |
| SST Framework                     | Simulation support       |
| GTest                             | Unit testing             |
| *IF using Rust for timing models* |
| Rust/Cargo                        | Timing model compilation |

### Configuration Options

| Option             | Default   | Description                                |
| ------------------ | --------- | ------------------------------------------ |
| `USE_SST`          | `ON`      | Build with SST simulation support (ON/OFF) |
| `CMAKE_BUILD_TYPE` | `Release` | Build type (Debug/Release)                 |

### Targets

| Target                     | Description                                                                                 |
| -------------------------- | ------------------------------------------------------------------------------------------- |
| `libdrra.so`               | Main project target that builds all components SST behavioral models into a shared library  |
| `test_timingModel`         | Tests for the SST timing model representation                                               |
| Component-specific targets | Multiple targets for individual components (defined by optional component-level CMakeLists) |

## Directory Structure

```shell
drra-components/
├── CMakeLists.txt            # Top-level CMakeLists
├── cmake/modules/            # CMake Modules  
│   ├── FindDRRAUtils.cmake
│   └── FindSST.cmake
├── lib/                      # Library of components
│   ├── cells/                  # Cells
│   ├── CMakeLists.txt          # Library-level CMakeLists
│   ├── common/                 # Common components and models
│   │   ├── io_buffer/
│   │   └── sst/
│   ├── controllers/            # Controllers
│   │   └── sequencer/
│   ├── fabric/                 # Fabric
│   └── resources/              # Resources
│       ├── dpu/
│       ├── rf/
│       ├── swb/
│       └── ...
└── sst_tests/               # SST-based tests
```

## Component Structure

Each component follows a standard structure.

```shell
<component_type>/<component_name>/  # e.g., resources/dpu/
├── CMakeLists.txt                  # Component-level CMakeLists
├── sst/                            # SST integration (optional)
│   └── CMakeLists.txt                # SST-level CMa
└── timing_model/                   # Rust timing model (optional)
    ├── Cargo.toml
    └── src/
        └── main.rs
```

## CMake Modules

Two helper functions are provided in CMake modules to help build software for components: [Rust/Cargo build](./CMakeBuildSystem.md#rust--cargo-build) and [SST build](./CMakeBuildSystem.md#sst-build).

### Rust / Cargo build

A `cargo_build()` function is provided to build Rust code.
For example, timing models of components can be implemented in Rust.
The project uses Corrosion to integrate Rust code with CMake.

!!! warning
    At the time of writing, Corrosion does not support building multiple targets
    with the same name. As our components all have `timing_model` Rust targets,
    we solve this issue by creating unique target identifiers.
    This could be changed in the future.
    Check [corrosion-rs/corrosion#394](https://github.com/corrosion-rs/corrosion/pull/394).

#### Usage

```cmake
find_package(DRRAUtils REQUIRED)
cargo_build(${CMAKE_CURRENT_SOURCE_DIR}/timing_model)
```

### SST build

A `sst_build()` function is provided to build SST models.

#### Usage

```cmake
find_package(SST REQUIRED)
sst_build()
```

The *SST model source file* is expected to use the same name as the component folder.
For example:

```shell
dpu/
├── CMakeLists.txt      # Component-level CMakeLists
└── sst
    ├── CMakeLists.txt  # SST-level CMakeLists
    ├── dpu.cpp         # SST model source file
    └── dpu.h
```

If the name of the source file is different, it can be overwritten using:

```cmake
find_package(SST REQUIRED)
sst_build(OVERRIDE_SOURCE_NAME "<source_file_name>")
```
