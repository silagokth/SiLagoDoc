# Installation

## Compile and Install from Source Code

### Prerequisites

Vesyla-suite can be compiled and installed on any modern linux distribution. Before compiling vesyla-suite, you need to install compilation tool chain. You need to use the correct package manager of your Linux distribution. The necessary packages include: g++ (version 5 or above), make, cmake, boost liabrires, gecode. Here, we give the example command for Fedora Linux.

```tcl
sudo dnf install g++ make cmake boost-devel perl autoconf libgmp-devel  
```

### Install Python3 and Python3 packages

Python3 is assential for vesyla-suite. You can install it according to the instruction on the official website: [https://www.python.org/](https://www.python.org/). You also need to install some python3 packages. Here, we give the example command for Fedora Linux.

```tcl
sudo dnf install python3-devel python3-pip
```

Vesyla-suite uses **Pyinstaller** to compile python3 scripts into native executable programs. You can install it by using pip3.

```tcl
pip3 install pyinstaller
```

### Compile and install vesyla-suite

You can download the **vesyla-suite** source package and make a build directory.

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

Now, all executable programs in vesyla-suite are installed.

### Post-installation Setup

If vesyla-suite are installed in non-standard location, you need to set the following environment variables to make it work.

```tcl
export PATH=$PATH:/path/to/vesyla-suite/bin
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/path/to/vesyla-suite/lib
```
