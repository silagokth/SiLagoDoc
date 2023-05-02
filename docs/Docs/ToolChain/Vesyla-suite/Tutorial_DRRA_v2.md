# DRRA-based AlImp Design Tutorial (v2)

!!! note
    This page is written for vesyla-suite **version 2**. It's deprecated and will be removed soon. You should only use it for old project such as DRRA characterization. For vesyla-suite version 3, please see [Tutorial](../Tutorial_DRRA).

## Introduction

Vesyla-suite provides two commands: vesyla and manas. Vesyla is used for compiling Matlab code to DRRA and DiMArch based SiLago fabric. Manas is used for instruction scheduling and generating RTL simulation environment.

## Write the source code

Please Check the [Vesyla Programming Guide v2](../VesylaProgrammingGuide_v2/).

## Compilation

To compile a program that is purely matlab script by using Vesyla and Manas, use the following command:

```tcl
vesyla -o $PathToOutputDirectory $PathToMatlabFile
manas -o $PathToOutputDirectory -t json $PathToOutputDirectory/filegen
```

## RTL simulation

Vesyla also generates environment for RTL simulation. It's in *\$OUTPUT/filegen/sim_vsim*. However, you can't directly simulate it under QuestaSim because you need to specify the path to the DRRA+DiMArch fabric.

To specify the path to the fabric, use the command:

```tcl
export FABRIC_PATH=$PathToFabric
```

Then, you can use Questasim to run the simulation script genereated by Vesyla:

```tcl
cd $OUTPUT/filegen/
vsim -c -do run_gui.do
```