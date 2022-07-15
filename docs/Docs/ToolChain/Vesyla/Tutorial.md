# Vesyla-suite tutorial

## Installation

Vesyla-suite can be compiled and installed on any modern linux distribution. Before compiling vesyla-suite, you need to install compilation tool chain. You need to use the correct package manager of your Linux distribution. The necessary packages include: g++ (version 5 or above), make, cmake, boost liabrires, gecode. Here, we give the example command for Fedora Linux.

```tcl
sudo yum install g++ make cmake boost-devel
```

Gecode is not really necessary, Vesyla only use it for CP scheduling engine. However, you still need to install it. You can install it according to the installation instruction on the official website: [https://www.gecode.org/](https://www.gecode.org/). Please install Gecode in stanard location (such as `/usr` or `/usr/local`) so that the cmake can find it.

```tcl
cd /gecode/root/folder
./configure --prefix=/desired/install/path
make
make install
```

Then, you download the **vesyla-suite** source package and make a build directory.

```tcl
cd /vesyla-suite/root/folder
mkdir build
```

Next step is to generate makefile according to the CMake configuration file CMakeLists.txt and specify the installation path. Once the makefile is generated, you can compile it and install the compiled program.

```tcl
cd build
cmake -DCMAKE_INSTALL_PREFIX=/desired/install/path ..
make
make install
```

Now, all executable programs in vesyla-suite are installed. If vesyla-suite are installed in non-standard location, you need to set the following environment variables to make it work.

```tcl
export PATH=$PATH:/path/to/vesyla-suite/bin
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/path/to/vesyla-suite/lib
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/path/to/gecode/lib
```

## Write the source code

Please Check the [Programming Guide](../ProgrammingGuide/).

## Compilation and simulation

### Use automatic test framework

!!! Warning
	Not available in public github repo

A bash script called *\$VesylaRoot/autotest/testall.sh* is provided to automatically test many testcases. Put all your testcases (matlab file) in *\$VesylaRoot/autotest/testcases/* directory, the script will test all of them and generate a report in the same directory. The script uses **Robot Framework** to manage the test process and to generate result. To run the test script, use command:

```tcl
./testall
```

### Use automatic test script to test single testcase
Script using test framework can test many testcases, but it will not print out detailed information. If you want to test single testcases, there is a provided bash script. It automatically compile your matlab file, prepare simulation environment and perform the matlab and RTL simulation, and finally compare the result. The script is called *\$VesylaRoot/autotest/run.sh*. To use it, use the command below:

```tcl
./run -c -f $PathToSiLagoFabric $PathToYourMatlabFile
```

```-c``` parameter try to compile Vesyla everytime in case Vesyla is modified, ```-f $PathToSiLagoFabric``` parameter indicates the RTL description of the SiLago fabric used for RTL simulation.

After the script has been executed, a message ```SUCCESS``` or ```FAIL``` will show at the end to report whether the source file is correct or not.

### Manually testing

#### Compile testcase

To compile a testcase that is purely matlab script by using Vesyla, go to the *build* directory and run:

```tcl
./vesyla -o $PathToOutputDirectory $PathToMatlabFile
```

If instead, you want to compile a testcase that consists a template file and a data file, go to the *build* directory and run:

```tcl
./vesyla -o $PathToOutputDirectory $PathToTemplate $PathToData
```

#### Matlab simulation

Vesyla generates a matlab simulation environment inside the output directory specified when you compile the testcase. Suppose your output directory is *\$OUTPUT*, the matlab simulation environment is in *\$OUTPUT/filegen/sim_matlab*. You can simulate the matlab model named as **instrumenteed_code.m** in matlab and get the register file data change event recording from the generated files after simulation.

#### QuestaSim simulation

Vesyla also generates environment for RTL simulation. It's in *\$OUTPUT/filegen/sim_vsim*. However, you can't directly simulate it under QuestaSim because you need to specify the path to the DRRA+DiMArch fabric. You need to change the automatic script according to the actual fabric RTL location in order to make the simulation work. Please check the provided automatic test script *\$ROOT/autotest/run.sh* to know more about how to add the fabric information. After QuestaSim simulation, a register file data change event recording will also be generated.

#### Compare the register file data change

A way to verify the correct behaviour of vesyla is to compare the register file data change event recording generated from both matlab and RTL simulation. A program for the exact purpose is created while vesyla is compiled. To run the program, go to *build* directory and use command:

```tcl
./vesyla_verify $PathToMatlabRecording $PathToVsimRecording
```

## Important output files

!!! warning
    Update later.
