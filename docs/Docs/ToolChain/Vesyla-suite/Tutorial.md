# Tutorial (v3)

!!! note
    This page is written for vesyla-suite **version 3**. For vesyla-suite version 2, please see [Tutorial v2](../Tutorial_v2).

## Introduction

## Programming Model

Each algorithm compiled by vesyla-suite will be mapped to a DRRA fabric. The DRRA fabric has a globally addressable input buffer and a globally addressable output buffer, as shown in the following figure.

![Programming Model](../Tutorial/programming_model.png)

The input buffer is used to store the input data of the algorithm. The output buffer is used to store the output data of the algorithm. The input buffer and the output buffer are connected to the DRRA fabric through the input and output ports of the fabric. The input and output ports are used to connect the DRRA fabric to the outside world.

Currently, we assume that all DRRA cells have access to both input and output buffer. This assumption might be modified in the future when introducing SiLago 2 DRRA fabric. The input bandwidth and output bandwith are determined by the number of columns of the DRRA fabric.

Right now, we don't support scratch-pad memory implemented by DiMArch rows. This will change when migrating to SiLago 2 DRRA fabric.

The assumption of giant globally addressable memory buffers is not realistic. However, these buffers will not be implemented as it is. Instead, application-level synthesis (ALS) tool will synthesize the input and output buffers to the actual hardware. The input and output buffers are used to simplify the algorithmic compilation process.

## Initialization

In any directory, you can initialize a vesyla-suite project by using the command:

```tcl
vs-init -s vs-vesyla
```

If this directory has already been initialized, you can force the re-initialization by using the command:

```tcl
vs-init -f -s vs-vesyla
```

You will notice that several files has been created in this directory. One of the files is ``config.json``. This file contains the configuration of the vesyla-suite project. You can modify this file to change the configuration of the project. The configuration file is described in the following section.

Another file you need to modify is ``main.cpp.jinja2``. This file is a template file used to generate the ``main.cpp`` file. You need to define some of the functions in this file. The functions are described in the following section.

## Implementation

In ``config.json``, you can define the hardware architecture of the DRRA fabric.

In ``main.cpp.jinja2``, you need to implement the following functions:

* ``void init()``: This function is used to initialize the input buffer.
* ``void model_l0()``: This function is used to implement the algorithm in the level 0 model. It's a pure software implementation of the algorithm. It's used to verify the correctness of the algorithm.
* ``void model_l1()``: This function is used to implement the algorithm in the level 1 model. It will be the input of vesyla-suite compiler. It's a software implementation of the algorithm with some hardware primitives. For syntax, plaese refer to [Vesyla Programming Guide](../VesylaProgrammingGuide).


## Simulation and Verification

To simulate the algorithm, simply run:

```tcl
./run.sh
```

If the output shows the simulation is successful, then the algorithm is correct.