# Installation and Usage

Vesyla and its dependency packages have to be installed on your system to use the tool chain.
The following sections describe how to install Vesyla and its dependency packages.

## Vesyla Installation

Vesyla is currently hosted on GitHub at [silagokth/vesyla](https://github.com/silagokth/vesyla).

### Installing

Vesyla is available as an AppImage release that can be downloaded from the [Github Releases](https://github.com/silagokth/vesyla/releases).
The AppImage file should be placed in PATH.
For example, in the _/usr/bin_ directory.

```bash
sudo mv vesyla /usr/bin/vesyla
```

### Building from source

Vesyla can be compiled and installed on any modern linux distribution.
Scripts are tested on Ubuntu 22.04 but should be compatible with recent versions of Ubuntu, Debian, Fedora and Arch.

#### 1. Prerequisites

```bash
sh ./scripts/install_dependencies.sh
```

#### 2. Compile Vesyla AppImage

```bash
sh ./scripts/make_appimage.sh
```

#### 3. Place AppImage in PATH

After you make the AppImage file, you should put the AppImage file in a directory in your PATH. For example, you can put the AppImage file in the _/usr/bin_ directory.

```bash
chmod +x ./vesyla
sudo mv vesyla /usr/bin/vesyla
```

## DRRA Components Library Installation

The DRRA components library is currently hosted on GitHub at [silagokth/drra-components](https://github.com/silagokth/drra-components).

### Installing

The DRRA components library is available as a compiled library that can be downloaded from the  [GitHub Releases](https://github.com/silagokth/drra-components/releases).
This library must be uncompressed and the following env. variable must be set:

```bash
tar -xvf library.tar.gz
export VESYLA_SUITE_PATH_COMPONENTS=$(pwd)/library
```

### Building from source

To set up the component library, you can clone the repository, compile it and set up the environment variable `VESYLA_SUITE_PATH_COMPONENTS` to point to the compiled library folder.

```bash
git clone git@github.com:silagokth/drra-components.git
cd drra-components
mkdir build
cd build
cmake ..
cmake --build .
export VESYLA_SUITE_PATH_COMPONENTS=$(pwd)/library
```

## Testing Installation (Optional)

Testcases are currently hosted on GitHub at [silagokth/drra-testcase](https://github.com/silagokth/drra-testcase).

You can clone the repository and run the testcases to verify the installation.

```bash
git clone git@github.com:silagokth/drra-testcase.git
cd drra-testcase
./run.sh
```

The execution results will be in the _output_ folder.
You can check the results to verify the installation.
