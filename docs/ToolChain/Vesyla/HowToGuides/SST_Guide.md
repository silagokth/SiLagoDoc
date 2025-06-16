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
  Refer to the [_mandatory_ code example](../Reference/SST/BU_example_mandatory.md).
- _important_: these sections are required to make the component functional and
  usable in an SST simulation.
  Implementing these sections will allow you to create a component
  and use it in an SST simulation.
  Refer to the [_important_ code example](../Reference/SST/BU_example_important.md).
- _useful_: these sections are used to enhance the component's functionality
  and usability in an SST simulation, but they are not strictly required.
  Implementing these sections will make the component more user-friendly and
  easier to use in simulations.
  Refer to the [_useful_ code example](../Reference/SST/BU_example_useful.md).

## Code structure

We take the example of the butterfly unit (bu) component.
Here is the code structure for the SST component:

```bash
./lib/resources/bu/sst/
├── CMakeLists.txt
├── bu.cpp
└── bu.h
```

## Header file declarations (_mandatory_)

The header file (`bu.h`) must include the _mandatory_ declarations:

- Calls to SST to register the component in the SST library.
- Declarations of the SST Simulation functions
  (i.e., `init`, `setup`, `complete`, `finish`).
  These functions will have to also be implemented in the source file (`bu.cpp`).
- Declaration and implementation of the DRRA instruction decoding function
  (i.e., `void decodeInstr(uint32_t)`).
- Class declaration, constructor and destructor.

More details on these declarations are provided in the following sections.

## SST Library Info (_mandatory_)

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

## SST Library Parameters (_mandatory_)

### SST Library parameters declaration (_mandatory_)

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

### SST Library parameters implementation (_important_)

**TODO: find another example for custom parameter as the data buffers are now included in drra.h**

Parameters are specified by the user in the SST simulation script.
They can be used in the component implementation to configure its behavior.
Here is an example of how to retrieve a parameter value in the component:

- Create a private member variable to store the parameter value in the header file:

  ```cpp
  private:
    /* Data buffers */
    uint32_t num_data_buffers = 6; // Default number of data buffers
  ```

- Retrieve the parameter value in the constructor of the component:

  ```cpp
  BU(SST::ComponentId_t id, SST::Params& params) : SST::Component(id) {
    // Retrieve the parameter value
    num_data_buffers = params.find<uint32_t>("num_data_buffers", 6);
    // The second argument is the default value if the parameter is not specified
  }
  ```

## SST Library Ports (_mandatory_)

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

## SST Library Statistics and Subcomponents (_mandatory_)

The DRRA header file (`drra.h`) does not provide any base statistics or subcomponents,
however, they can be added to the component if needed.
Refer to the [SST documentation](https://sst-simulator.org/sst-docs/) for more information.

```cpp
// Empty calls to SST statistics and subcomponents
SST_ELI_DOCUMENT_STATISTICS()
SST_ELI_DOCUMENT_SUBCOMPONENT_SLOTS()
```

## SST Simulation functions (_mandatory_)

SST components must implement 4 mandatory SST functions that are called
by the SST simulation engine at different stages of the simulation:

- `void BU::init(unsigned int phase)`: Called at the beginning of the simulation.
- `void BU::setup()`: Called after all components have been initialized.
- `void BU::complete(unsigned int phase)`:
  Called after all components have been set up.
- `void BU::finish()`: Called at the end of the simulation.

To learn more about these functions, refer to the [SST documentation](https://sst-simulator.org/sst-docs/).

## DRRA Instruction Decoding function

### Instruction Decoding Declaration (_mandatory_)

SST components for the DRRA platform must implement a function to decode
instructions, following this declaration:

```cpp
void decodeInstr(uint32_t instr);
```

### Instruction Decoding Implementation (_important_)

The DRRA header file (`drra.h`) provides functions to simplify the decoding of instructions:

- `uint32_t getInstrOpcode(uint32_t instr)`: Returns the opcode of the instruction.
- `uint32_t getInstrField(uint32_t instr, uint32_t fieldWidth, uint32_t fieldOffset)`:
  Returns the value of a specific field in the instruction with a given bit width
  (`fieldWidth`) and offset (`fieldOffset`, pointing to the least significant bit
  of the field).

Refer to the [_important_ code example](../Reference/SST/BU_example_important.md)
for more details on how to implement the `decodeInstr` function.

## SST Clock Handler function (_important_)

### Clock Handler Declaration (_important_)

SST components should implement a clock tick function to react to
clock events during the simulation.
The header file should declare the function as public as follows:

```cpp
bool clockTick(SST::Cycle_t currentCycle) override;
```

### Clock Handler Implementation (_important_)

The DRRA header file (`drra.h`) provides a function to simplify the clock handler
implementation.
This function should be called at the beginning of the `clockTick` function:

- `void executeScheduledEventsForCycle(SST::Cycle_t currentCycle)`:
  Executes all scheduled events for the current cycle.

The clock handler function should return `false` by default to end a cycle,
and `true` if the component has finished its full simulation process.

## DRRA Data communication between components (_important_)

SST components can exchange data with each other using **ports**,
connected together through **links**.
DRRA components can use the `DataEvent` class to send and receive data
through SST ports and links.
The `DataEvent` class is defined in the `dataEvent.h` header file.
Here is an example of how to use the `DataEvent` class to receive input data.

Data received by the component can be stored in a buffer.
By default --- as declared in the DRRA header file (`drra.h`) ---
each slot of the component has a buffer to store data (i.e., `data_buffers[slot_id]`).

1. Declare a public function in the header file to handle the Events:

   ```cpp
   public:
    ...
    /* DRRA Event Handler */
    void handleEventWithSlotID(SST::Event *event, uint32_t slot_id);
   ```

2. Add a call to the `handleEventWithSlotID` function in the clock handler function
   to handle the events received on the ports:

   ```cpp
   bool BU::clockTick(SST::Cycle_t currentCycle) {
     executeScheduledEventsForCycle(currentCycle);

     // Handle Data Events
     for (int i = 0; i < resource_size; i++) {
       Event *event = data_links[i]->recv();
       if (event) {
         handleEventWithSlotID(event, i);
       }
     }

     return false;
   }
   ```

3. Implement the `handleEventWithSlotID` function to handle the received events:

   ```cpp
   void BU::handleEventWithSlotID(SST::Event *event, uint32_t slot_id) {
     // Check if the event is a DataEvent
     DataEvent *dataEvent = dynamic_cast<DataEvent *>(event);
     if (dataEvent) {
       // Only handle events on the write narrow port
       if (dataEvent->portType != DataEvent::PortType::WriteNarrow)
         out.fatal(CALL_INFO, -1, "Invalid port type\n");

       // Store the data in the buffer of the slot
       out.output("Received data (slot=%d, size=%dbits, data=%s)\n", slot_id,
                 dataEvent->size,
                 formatRawDataToWords(dataEvent->payload).c_str());

       // Retrieve the data from the event
       std::vector<uint8_t> data = dataEvent->payload;

       // Store the data in the slot buffer
       data_buffers[slot_id] = data;
     }
   }
   ```
