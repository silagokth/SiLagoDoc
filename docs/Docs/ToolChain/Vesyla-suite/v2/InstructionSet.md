# Instruction Set (v2)

!!! warning
    This is an old version of the instruction set. Please refer to [Instruction Set (v3)](../../v3/InstructionSet) for the latest version.

## Instructions

--8<-- "Docs/ToolChain/Vesyla-suite/v2/InstructionSet/isa.md"

## ISA Description File

### JSON Schema

The ISA description file uses *json* format and validated by the following json schema:

```json
--8<-- "Docs/ToolChain/Vesyla-suite/v2/InstructionSet/isa_schema.json"
```

!!! Note
    You can validate a ISA description json file using this schema on [Json Schema Validator](https://www.jsonschemavalidator.net/).

### JSON

The ISA description file used for DRRA is shown as follow:

```json
--8<-- "Docs/ToolChain/Vesyla-suite/v2/InstructionSet/isa.json"
```
