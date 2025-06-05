# How to write a behavioral model for a new component using SST

This guide will help you write an SST component for the DRRA platform.
While it details some of the steps to create an SST component,
it will not cover many of the SST concepts that are not used in the DRRA platform.
To learn more about how to create an SST component unrelated to the DRRA platform,
please refer to the official [SST documentation](https://sst-simulator.org/sst-docs/).

Sections of this guide are annotated based on their importance:

- _mandatory_: these sections are required for the component to correctly build
  and be registered in the SST library (i.e., appearing when running `sst-info`).
  Implementing only these sections will allow you to create a basic component,
  but it will not be functional for simulation.
- _important_: these sections are required to make the component functional and
  usable in an SST simulation.
  Implementing these sections will allow you to create a component
  and use it in an SST simulation.
- _useful_: these sections are used to enhance the component's functionality
  and usability in an SST simulation, but they are not strictly required.
  Implementing these sections will make the component more user-friendly and
  easier to use in simulations.

## Code structure (_mandatory_)

**We take the example of the butterfly unit (bu) component.**

- Create an SST folder that contains:

```bash
./lib/resources/bu/sst/
├── CMakeLists.txt
├── bu.cpp
└── bu.h
```

## Header file declarations

The header file (`bu.h`) must include the necessary declarations:

- Calls to SST to register the component in the SST library.
- Declarations of the SST Simulation functions
  (i.e., `init`, `setup`, `complete`, `finish`).
  These functions will have to also be implemented in the source file (`bu.cpp`).
- Class declaration, constructor and destructor.

### SST Library Info (_mandatory_)

For the component to be recognized by the SST library,
we need to provide some metadata in the header file.
The declaration includes the following:

- Component class name: The same as the class name we are going to define.
- Library name: The library where the component will be placed (in this case "drra").
- Component name: The name of the component (in this case "bu").
  This name will be used to instantiate the component using SST.
- Component version: The version of the component, respecting semantic versioning
  (major.minor.patch).
- Component description: A brief description of the component.
- Component category: The category of the component,
  which is used to group components in the SST library.
  This has no effect on the functionality of the component.
  The different categories (as of SST 15.0.0) are:
  - `COMPONENT_CATEGORY_UNCATEGORIZED`
  - `COMPONENT_CATEGORY_PROCESSOR`
  - `COMPONENT_CATEGORY_MEMORY`
  - `COMPONENT_CATEGORY_NETWORK`
  - `COMPONENT_CATEGORY_SYSTEM`

```cpp
SST_ELI_REGISTER_COMPONENT(
  BU, // component class
  "drra", // library name
  "bu", // component name
  SST_ELI_ELEMENT_VERSION(1, 0, 0), // component version
  "Butterfly Unit component", // component description
  COMPONENT_CATEGORY_PROCESSOR // component category
)
```

### SST Library Parameters (_mandatory_)

The DRRA header file (`drra.h`) already provides base parameters,
every DRRA component can use those parameters in their implementation.
For a complete list of base parameters, refer to the [DRRA SST base component configuration](../Reference/SST/DRRA_SST_base.md).

```cpp
static std::vector<SST::ElementInfoParam> getComponentParams() {
  auto params = DRRAResource::getBaseParams();
  // Additional parameters can be added here
  // params.push_back(
  //   {"example_param", "Example param description", "default_value"}
  // );
  return params;
}
SST_ELI_DOCUMENT_PARAMS(getComponentParams())
```

### SST Library Ports (_mandatory_)

The DRRA header file (`drra.h`) already provides base ports,
every DRRA component can use those ports in their implementation.
For a complete list of base ports, refer to the [DRRA SST base component configuration](../Reference/SST/DRRA_SST_base.md).

```cpp
static std::vector<SST::ElementInfoPort> getComponentPorts() {
  auto ports = DRRAResource::getBasePorts();
  // Additional ports can be added here
  // ports.push_back({"example_port", "Example port description"});
  return ports;
}
SST_ELI_DOCUMENT_PORTS(getComponentPorts())
```

### SST Library Statistics and Subcomponents (_mandatory_)

The DRRA header file (`drra.h`) does not provide any base statistics or subcomponents,
however, they can be added to the component if needed.
Refer to the [SST documentation](https://sst-simulator.org/sst-docs/) for more information.

```cpp
// Empty calls to SST statistics and subcomponents
SST_ELI_DOCUMENT_STATISTICS()
SST_ELI_DOCUMENT_SUBCOMPONENT_SLOTS()
```

### SST Simulation functions (_mandatory_)

SST components must implement 4 mandatory SST functions that are called
by the SST simulation engine at different stages of the simulation:

- `void BU::init(unsigned int phase)`: Called at the beginning of the simulation.
- `void BU::setup()`: Called after all components have been initialized.
- `void BU::complete(unsigned int phase)`:
  Called after all components have been set up.
- `void BU::finish()`: Called at the end of the simulation.

To learn more about these functions, refer to the [SST documentation](https://sst-simulator.org/sst-docs/).

### DRRA Instruction Decoding function (_mandatory_)
