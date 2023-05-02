# Vesyla-suite Overview

## Version 2

Vesyla-suite version 2 is used for compiling **Matlab** code to **DRRA and DiMArch** based SiLago fabric. It does not support general input and output buffer for application-level synthesis. This version is deprecated and will be removed soon. You should only use it for old project such as DRRA characterization.

### Tool collection

* vesyla
* manas

### Usage

#### Installation on Mozzarella

Vesyla-suite version 2 has already been installed on Mozzarella. Since it's a deprecated version, you shouldn't try to compile it by yourself. Just use the installed instance on Mozzarella.

To access vesyla-suite commands, you need to first load the vesyla-suite module by command:

```tcl
module load vesyla-suite/2
```

Now you should be able to access ``vesyla`` and ``manas`` command.

### Tutorial

See [Turorial v2](../Tutorial_v2) for vesyla and manas version 2.

### Programming Guide

See [Vesyla Programming Guide v2](../VesylaProgrammingGuide_v2)

## Version 3

### Tool collection

* vs-vesyla
* vs-manas
* vs-alimpsim
* vs-init
* vs-expand
* vs-imecc

### Installation and Usage

#### Installation on Mozzarella

Vesyla-suite version 3 has already been installed on Mozzarella.

To access vesyla-suite commands, you need to first load the vesyla-suite module by command:

```tcl
module load vesyla-suite/3
```

#### Docker

See [Installation](../Installation#create-and-use-docker-image)

#### Compile from Source

See [Installation](../Installation#compile-and-install-from-source-code)

### Tutorial

See [DRRA-based AlImp Design Tutorial](../Tutorial_DRRA) and [RISCV-based AlImp Design Tutorial](../Tutorial_RISCV).

### Programming Guide

See [Vesyla Programming Guide](../VesylaProgrammingGuide)