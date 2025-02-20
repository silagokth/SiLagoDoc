# Architecture Description

## Main Concepts

### Resources

Resources are the basic building blocks for implement vector processing. Typical resources include register files, SRAMs, IOs, and DPUs. Each resource can occupy a number of slots. The interconnection interface attached on each resource is standardized. They can be either word level or bulk level. Word level interface is used for intra-cell communication, while bulk level interface is used for inter-cell communication.

A slot is the basic "place-holder" for a resource. It can be either occupied or unoccupied. Each slot contains a instruction decoder, a function implementation space, a word-level input port, a word-level output port, a bulk-level input port and a bulk-level output port.

A resource can occupy one or more continuous slots. The number of slots occupied by a resource is called the resource size. The resource size is determined by several factors, including the number of input/output ports, the complexity of the function logic, etc.

### Controllers

A controller is the building block that manages the resources in a cell. It issues instructions into the slots of the resources. The controller can have many flavors, varying its instruction memory size, maximum slot number, RACCU register width, etc.

### Cells

Each cell consists of a controller and one or more resources. The controller manages the resources in the cell. The resources can be either homogeneous or heterogeneous. Only bulk-level interface is visible between cells.

### Fabric

The fabric is the collection of cells. The cells are connected together using bulk-level interface. The fabric can be either homogeneous or heterogeneous. The cells in a fabric is arranged in a 2-D mesh style.

## JSON Schema

<!-- prettier-ignore -->
!!! tip "Validation"
    You can validate a architecture description json file using this schema on [Json Schema Validator](https://www.jsonschemavalidator.net/).

The architecture description file uses _json_ format and validated by the following json schema:

```json
--8<-- "Docs/ToolChain/Vesyla-suite/v4/Architecture/arch_schema.json"
```
