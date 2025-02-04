# Installation

!!! note
    This page is written for vesyla-suite **version 3**. We don't recommend to compile for vesyla-suite version 2.

## Create and Use Docker Image

### Prerequisites

To use docker image, you need to install docker first. You can install docker according to the instruction on the official website: [https://docs.docker.com/install/](https://docs.docker.com/install/).

### Build Docker Image

There is a docker file in the root folder of vesyla-suite. You can use it to create a docker image for vesyla-suite. The docker image is based on Fedora Linux. You can use the following command to create a docker image (assuming the version is 3.0):

```tcl
docker build -t vesyla-suite:3.0 .
```

Optionally, you can export the docker image to a tar file by using the following command:

```tcl
docker save -o vesyla-suite-3.0.tar vesyla-suite:3.0
```

You don't have to build your own docker image from the source code. It is also possible to download the docker image from one-drive. Ask the vesyla-suite maintainer for the download link. Once you can import the docker image using the command:

```tcl
docker load -i vesyla-suite-3.0.tar
```
### Create Docker Container

To create a docker container from the docker image and bind the current directory to the `/work` directory in the container, you can use the following command:

```tcl
docker run --name vesyla-suite -v $PWD:/work vesyla-suite:3.0
```

### Use Docker Container

Once the container has been created, you can use the following command to enter the container and access the commands provided by vesyla-suite:

```tcl
docker start -i vesyla-suite
```

## Compile and Install from Source Code

### Prerequisites

Vesyla-suite can be compiled and installed on any modern linux distribution. Before compiling vesyla-suite, you need to install compilation tool chain. You need to use the correct package manager of your Linux distribution. The necessary packages include: g++ (version 5 or above), make, cmake, boost liabrires, gecode. Here, we give the example command for Fedora Linux.

```tcl
sudo dnf install g++ make cmake boost-devel perl autoconf libgmp-devel  
```

### Installation Gecode

Vesyla uses Gecode it for CP scheduling engine. You can install it according to the installation instruction on the official website: [https://www.gecode.org/](https://www.gecode.org/). Please install Gecode in stanard location (such as `/usr` or `/usr/local`) so that the cmake can find it.

```tcl
cd /gecode/root/folder
./configure --prefix=/desired/install/path
make
make install
```

### Install Python3 and Python3 packages

Python3 is assential for vesyla-suite. You can install it according to the instruction on the official website: [https://www.python.org/](https://www.python.org/). You also need to install some python3 packages. Here, we give the example command for Fedora Linux.

```tcl
sudo dnf install python3-devel python3-pip
```

Vesyla-suite uses **nuitka** to compile python3 scripts into native executable programs. You can install it by using pip3.

```tcl
sudo dnf install patchelf
pip3 install nuitka
```

### Compile and install vesyla-suite

Vesyla-suite also need flex and bison to compile. You can install them by using the package manager of your Linux distribution. Here, we give the example command for Fedora Linux.

```tcl
sudo dnf install bison flex
```

Then, you can download the **vesyla-suite** source package and make a build directory.

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
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/path/to/gecode/lib
```
