# Repo Organization

Vesyla eco-system is constructed by many modules. One can create a new app in this eco-system by combining several modules.

## Module Type

### Executable Module

The executable modules create executable applications. If written in C++, it will be compiled to an executable binary file.

### Data Module

The data modules define classes for various data structures. In vesyla eco-system, we encourage developer to define pure data structure using descriptive language or library. The [protobuf (version 3)](https://developers.google.com/protocol-buffers) from Google is a very good libarary for such purpose because it's language-neutral and platform-neutral.

Besides the pure data structure, developers should also include a handler class to provide a data access interface. Several programming language specific interfaces could be provided.

### Process Module

The process modules manupilate the data structer defined by other data modules. The process modules usually implement one or more algorithms to refine the data structure.

### Utility Module

The utility modules include the basic functionality that is needed by the majority of other modules, such as event logging, configuration file searching, and global variable mantainance.

## Module Implementation

Each module is implemented by [github submodule](https://github.blog/2016-02-01-working-with-submodules/) function.

## Module Testing

Each module should have a top-level folder for testing. For executable module, the tests could be overall general testcases. We recommend to use the [Robot Framework](https://robotframework.org/) to automatically run all testcases. For other type of modules, the tests are mostly unit-tests. C++ based modules can easily be tested via standard [boost test library](https://www.boost.org/doc/libs/1_79_0/libs/test/doc/html/index.html)

## Organization

The top-level repo is called **vesyla suite**. It contains all the other repo as modules. The flat and shallow dependency strucutre makes it easier to manage. There are two major executable modules: **vesyla** and **manas**.

## Branch

We recommend to use two branches in each repo: **master** and **develop**. The **master** branch keeps stable version of the repo and the **develop** branch is for adding new features and fixing bugs. The **develop** branch should be merged/rebased to the **master** branch at least once per month.