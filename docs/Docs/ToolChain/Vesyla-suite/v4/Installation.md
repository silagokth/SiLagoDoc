# Installation and Usage

Vesyla-suite and its dependency packages have to be installed on your system to use the tool chain. The following sections describe how to install vesyla-suite and its dependency packages.

## Vesyla-suite

### Install By Downloading the Release

!!! tip "Recommendation"

Vesyla-suite is available as an AppImage. You can download the AppImage file of a specific release from the [github page](https://github.com/silagokth/vesyla-suite-4/releases). You should put the AppImage file in a directory in your PATH. For example, you can put the AppImage file in the _/usr/bin_ directory.

```tcl
sudo mv vesyla-suite /usr/bin/vesyla-suite
```

### Install From Source Code

If you want to compile and install vesyla-suite from source code and make the AppImage file, you can follow the instructions below.

#### Prerequisites

Vesyla-suite can be compiled and installed on any modern linux distribution. Before compiling vesyla-suite, you need to install compilation tool chain. You need to use the correct package manager of your Linux distribution. Read the _requirements.txt_ file in the vesyla-suite source package to get the detailed information about the required packages.

We also provide a script for major Linux distributions to install the required packages automatically. You can find the script in the vesyla-suite source package. The script is named _install_dependencies.sh_. You can run the script to install the required packages.

```tcl
./install_dependencies.sh
```

#### Compile and install vesyla-suite

We provide a simple script to compile vesyla-suite and make the AppImage file. You can find the script in the vesyla-suite source package. The script is named _make_appimage.sh_. You can run the script to compile vesyla-suite and make the AppImage file.

```tcl
./make_appimage.sh
```

#### Put the AppImage file in your PATH

After you make the AppImage file, you should put the AppImage file in a directory in your PATH. For example, you can put the AppImage file in the _/usr/bin_ directory.

```tcl
sudo mv vesyla-suite /usr/bin/vesyla-suite
```

## DRRA Component Library

### Install By Downloading the Release

!!! tip "Recommendation"

The DRRA component library is currently hosted on github at [drra-component-lib](https://github.com/silagokth/drra-component-lib). In the _lib_ folder, you can find the source code of components for fabric, cells, controllers as well as resources. To use the component library, you can download the release from the [github release page](https://github.com/silagokth/drra-component-lib/releases) and decompress the tarball file.

```bash
tar -xvf library.tar.gz
export VESYLA_SUITE_PATH_COMPONENTS=$(pwd)/library
```

### Install From Source Code

To set up the component library, you can clone the repository, compile it and set up the environment variable `VESYLA_SUITE_PATH_COMPONENTS` to point to the compiled library folder.

```bash
git clone git@github.com:silagokth/drra-component-lib.git
cd drra-component-lib
mkdir build
cd build
cmake ..
make -j
export VESYLA_SUITE_PATH_COMPONENTS=$(pwd)/library
```

## Testcases (Optional)

You can optionally download and test all the testcases of DRRA-2. The testcases are available on the [github page](https://github.com/silagokth/drra-testcase). You can clone the repository and run the testcases to verify the installation.

```bash
git clone git@github.com:silagokth/drra-testcase.git
cd drra-testcase
./run.sh
```

The execution result will be in the _output_ folder. You can check the result to verify the installation.
