# Installation and Usage

## Cheddar

!!! tip "Recommendation"
    The easiest way to use vesyla-suite is to use the pre-installed version on Cheddar.

Vesyla-suite is pre-installed on Cheddar. You can use it directly without any installation. Contact the system administrator if you need an account on Cheddar.

Before using vesyla-suite, you need to load the environment module.

```tcl
module load vesyla-suite/4
```

## Docker Image

!!! warning "Irregular Updates"
    The docker image is not updated regularly. It may not contain the latest version of vesyla-suite.

Vesyla-suite is available as a docker image. The docker image is stored on SiLago SharePoint. Contact the system administrator if you need access to the docker image.

Once you have download the docker image, import it to your local docker environment.

```tcl
docker load -i vesyla-suite-4.tar
``` 

You can run the docker image and mount the local directory to the docker container as `/work` directory by the following command.

```tcl
docker run --name vesyla-suite -dit -v $PWD:/work vesyla-suite-4
```

Then you can start the docker container and use vesyla-suite.

```tcl
docker start -it vesyla-suite
```

## Source Code

If you want to compile and install vesyla-suite from source code, you can follow the instructions below.

### Prerequisites

Vesyla-suite can be compiled and installed on any modern linux distribution. Before compiling vesyla-suite, you need to install compilation tool chain. You need to use the correct package manager of your Linux distribution. The necessary packages include: g++ (version 5 or above), make, cmake, boost liabrires, gecode. Here, we give the example command for Fedora Linux.

```tcl
sudo dnf install g++ make cmake boost-devel protobuf
```

### Install Python3 and Python3 packages

Python3 is essential for vesyla-suite. You can install it according to the instruction on the official website: [https://www.python.org/](https://www.python.org/). You also need to install some python3 packages. Here, we give the example command for Fedora Linux.

```tcl
sudo dnf install python3-devel python3-pip
```

Vesyla-suite uses **Pyinstaller** to compile python3 scripts into native executable programs. You can install it by using pip3.

```tcl
pip3 install pyinstaller
```

Other required python packages can be installed by using pip3.

```tcl
pip3 install ortools protobuf=4.24.2 pyinstaller verboselogs coloredlogs numpy matplotlib binarytree sympy regex lark uuid
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
