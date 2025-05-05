# DRRA Extension System

The DRRA-2 is a template system. Each fabric instance is assembled from components in a library according to an architecture description. The DRRA-2 provides an extension system to allow users to add new components to the component library. The extension system is a simple way to add new components to the component library without rebuilding the complete compilation toolchain.

## Important Concepts

### Extension Type

The DRRA template system is divided into 4 types of hierarchical components: fabric, cells, controllers, and resources. The extension system allows users to add new components (cells, controllers, as well as resources) to the component library.

### Extension Package

To make an extension and add it to the component library, the user needs to create an extension package, which is just a folder containing the extension files, and place it inside the hierarchy of the component library. The library then need to be compiled again to include the new extension.

## Component Package Structure

The component package is a folder that contains the component files. The component package is placed in the hierarchy of the component library. It contains the following files:

- `arch.json`: The architectural description file of the component. This file is the foundation of the component.
- `isa.json`: The ISA description file of the component. This only applies to controllers and resources.
- `rtl.sv.j2`: The jinja _template_ of RTL description file of the component.
- `timing_model`: The directory that contains the source code to generate the executable file that can compute the timing model from the input instructions. This is used by instruction scheduler and instruction-level simulator.
- `behavioral_model`: The directory contains the source code that will be compiled into the instruction-level simulator of the fabric instance.
- `CMakeLists.txt`: The CMake file which directs the compilation and installation of the component package.
