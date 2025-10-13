# Installation and Usage

Vesyla and its dependency packages have to be installed on your system to use the tool chain.
The following sections describe how to install Vesyla and its dependency packages.

## Vesyla Installation

### Requirements

- [minizinc](https://www.minizinc.org/) (used by `vesyla compile`)
- `g++` (used by `vesyla testcase`)

### Download

Built packages are available for download in the [releases](https://github.com/silagokth/vesyla/releases).

### Install

- For AppImage or generic tar.gz package,
extract and copy to a location in your PATH.
Make sure to give execution permissions to the binary.

- For Debian-based systems:

   ```bash
   sudo dpkg -i pkg/vesyla-*.deb
   ```

- For RedHat-based systems:

   ```bash
   sudo rpm -i pkg/vesyla-*.rpm
   ```

### Check

Check the installation by running:

```bash
vesyla --version
```

## Compilation

### Dependencies

- CMake >= 3.22.1
- Clang >= 5 or GCC >= 9.0 (C++17 support)
- Flex and Bison (tested on 2.6.4 and 3.8.2, respectively)

### Build

   ```bash
   mkdir build
   cd build
   cmake ..
   cmake --build . -- -j$(nproc)
   ```

### Create and install package

Packages will be generated in the `build/pkg` directory.

- For Debian-based systems:

   ```bash
   cpack -G DEB
   sudo dpkg -i pkg/vesyla-*.deb
   ```

- For RedHat-based systems:

   ```bash
   cpack -G RPM
   sudo rpm -i pkg/vesyla-*.rpm
   ```

- For generic tar.gz package:

   ```bash
   cpack -G TGZ
   ```

   To install, extract the tarball and copy to a location in your PATH.

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

Testcases are currently hosted on GitHub at [silagokth/drra-tests](https://github.com/silagokth/drra-tests).

### Requirements

To install the requirements for running the testcases, you can use the provided script:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

### Run

```bash
vesyla testcase generate -d testcases
./run.sh
```
