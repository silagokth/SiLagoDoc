# Vesyla tutorial

## Working environment

- **OS**: Linux
- **Software**:
    - Matlab
    - QuestaSim

## File structure

```
$VesylaRoot/
  |- src/
  |    |- all source files.
  |
  |- config/
  |    |- some configuration files
  |
  |- autotest/
  |    |- some scripts for auto testing.
  |    |- testcases/
  |
  |- cmake/
  |- clean
  |- build/
       |- compilation working directory
```

## Compile Vesyla

First, install compilation tool chain. You need to use the correct package manager of your Linux distribution. The needed packages include: g++ (version 5 or above), make, cmake, boost liabrires, gecode. Here, we give the example command for Fedora Linux.

```tcl
sudo yum install g++ make cmake boost-devel
```

Gecode is not really necessary, Vesyla only use it for CP scheduling engine. You can install it according to the installation instruction on the official website: [https://www.gecode.org/](https://www.gecode.org/)

Then, you create working directory *build*.

```tcl
cd $VesylaRoot
mkdir build
cd build
```

Next step is to generate makefile according to the CMake configuration file CMakeLists.txt.

```tcl
cmake ..
```

Now, it's time to compile vesyla.

```tcl
make
```

After a while, the executable file named as "vesyla" should appear in the *build* directory.

## Write the source code

Please Check the [Programming Guide](../ProgrammingGuide/).

## Compilation and simulation

### Use automatic test framework
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

After the script has been executed, a message "SUCCESS" or "FAIL" will show at the end to report whether the source file is correct or not.

### Manually testing

!!! warning
    Too complicated to explain, update later.

## Important output files

!!! warning
    Update later.
