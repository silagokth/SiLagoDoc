# Installation and Usage

## AppImage

!!! tip "Recommendation"

Vesyla-suite is also available as an AppImage. You can download the AppImage file of a specific release from the [github page](https://github.com/silagokth/vesyla-suite-4/releases). You should put the AppImage file in a directory in your PATH. For example, you can put the AppImage file in the */usr/bin* directory.

```tcl
sudo mv vesyla-suite /usr/bin/vesyla-suite
```

## Source Code

If you want to compile and install vesyla-suite from source code and make the AppImage file, you can follow the instructions below.

### Prerequisites

Vesyla-suite can be compiled and installed on any modern linux distribution. Before compiling vesyla-suite, you need to install compilation tool chain. You need to use the correct package manager of your Linux distribution. Read the *requirements.txt* file in the vesyla-suite source package to get the detailed information about the required packages.

We also provide a script for major Linux distributions to install the required packages automatically. You can find the script in the vesyla-suite source package. The script is named *install_dependencies.sh*. You can run the script to install the required packages.

```tcl
./install_dependencies.sh
```

### Compile and install vesyla-suite

We provide a simple script to compile vesyla-suite and make the AppImage file. You can find the script in the vesyla-suite source package. The script is named *make_appimage.sh*. You can run the script to compile vesyla-suite and make the AppImage file.

```tcl
./make_appimage.sh
```

### Put the AppImage file in your PATH

After you make the AppImage file, you should put the AppImage file in a directory in your PATH. For example, you can put the AppImage file in the */usr/bin* directory.

```tcl
sudo mv vesyla-suite /usr/bin/vesyla-suite
```
