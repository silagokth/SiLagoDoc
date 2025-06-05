# SST DRRA base component configuration

SST parameters are a set of variables that can be set when instantiating a component
in the SST simulation configuration file (using python).
SST components can access these parameters in their implementation and use them
as configuration options.

SST ports are the connections between SST components that can be linked
when instantiating the component in the SST simulation configuration file.
SST components can access these ports in their implementation to exchange data
with other each other.

In the DRRA header SST library, we define a set of parameters that are used
by all DRRA components.
We call them "base parameters" as they constitute the foundation for DRRA
components and can be extended upon.
The base parameters differ between DRRA resources and controllers.

## DRRA Resource

### DRRA Resource Parameters

Here is a list of the base parameters for DRRA resources:

- `resource_size`: Number of slots occupied by the resource.
  Default is `1`.
- `slot_id`: The slot ID that defines the position of the resource in the fabric.
  If the resource occupies multiple slots (i.e., `resource_size > 1`),
  `slot_id` is the ID of the first slot occupied by the resource.
  This is a mandatory parameter.
- `has_io_input_connection` (or `has_io_output_connection`):
  Defines whether the resource has (`1`) or does not
  have (`0`) an input (or output) connection to the fabric.
  If the value is `1`, `slot_id` has to be `1` as only the first slot supports
  connections to IO buffers.
  Default is `0`.

### DRRA Resource Ports

Here is a list of the base ports for DRRA resources:

- `controller_port%(portnum)d`: Link(s) to the controller.
  There can be multiple links to the controller, one for each slot occupied
  by the resource.
  Each port is named `controller_port0`, `controller_port1`, etc.
- `data_port%(portnum)d`: Link(s) to exchange data with other resources.
  There can be multiple data ports, one for each slot occupied by the resource.
  Each port is named `data_port0`, `data_port1`, etc.
- `io_input_port`: Link to the IO input buffer.
  This port is only present if `has_io_input_connection` is set to `1` and if
  the resource occupies slot 1.
- `io_output_port`: Link to the IO output buffer.
  This port is only present if `has_io_output_connection` is set to `1` and if
  the resource occupies slot 1.

## DRRA Controller

### DRRA Controller Parameters

Here is a list of the base parameters for DRRA controllers:

- `num_slots`: Number of slots supported by the controller.
  Default is `16`.

### DRRA Controller Ports

Here is a list of the base ports for DRRA controllers:

- `slot_port%(portnum)d`: Link(s) to resources in the fabric's slots.
  There should be `num_slots` ports, each port can be linked to a resource.
  Each port is named `slot_port0`, `slot_port1`, ..., `slot_port%(num_slots-1)d`.
