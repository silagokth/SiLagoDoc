# IO for DRRA-2 Fabric

## Introduction

A DRRA-2 system consists of a global controller (implemented by RISC-V), several peripheral devices, and an acceleration fabric. The acceleration fabric or DRRA-2 fabric is a 2D mesh of cells. It requires at least 3 rows of cells. The first row hosts exclusively the IOSRAM that are used for temporarily store data loading from the input buffer. The last row hosts exclusively the IOSRAM that are used for temporarily store data storing to the output buffer. The middle rows are normal DRRA-2 cells that are used for computation or temporary storage.

## Non-deterministic Protocol

The communication between DRRA-2 cells and the input/output buffer are based on non-deterministic protocol to support the communication between dynamic algorithms via global NoCs. Here we use "reading data from input buffer to SRAM" as an example to illustrate the communication protocol.

The slot that can host IOSRAM are slot 1. It is hardwired. The IOSRAM will have the following communication wires:

- `addr`: The address input port. It is used to specify the address of the data to be read from the input buffer.
- `en`: The enable input port. It is used to enable the read operation.
- `valid`: The flag to indicate the data is ready to be read.
- `data`: The data input port. It is used to read data from the input buffer to the IOSRAM.

Two DSU instructions need to be issued. The first DSU instruction should be send to the slot 1 port 0. It will configure the FSM in port 0 to read data from the input buffer. The second DSU instruction should be send to the slot 1 port 2. It will configure the FSM in port 2 to put the data into the SRAM. The two instructions can also be wrapped by multiple REP instructions to form loop. The two instructions can be activated at the same time since the second DSU will not take effect immediately.

For example, if we need to read the data in address 0,1,2,3 from the input buffer to address 3,4,5,6 in IOSRAM, the first DSU instruction and its REP instructions should be:

```
rop <read_to_input> (slot=1, port=0){
    dsu (slot=1, port=0, addr_sd=0, addr=0)
    rep (slot=1, port=0, step=1, iter=4)
}
```

The second DSU instruction and its REP instructions should be:

```
rop <write_to_iosram> (slot=1, port=2){
    dsu (slot=1, port=2, addr_sd=0, addr=3)
    rep (slot=1, port=2, step=1, iter=4)
}
```

We need to activate the two instructions at the same time. So we need to define the constraint:

```
linear read_to_input == write_to_iosram
```

Now, the FSM in port 0 should generate the first address (0) and send it out to the `addr` signal and set the `en` signal to high. The FSM in port 1 will generate the first address (3), but it will not write anything into SRAM since the data is not ready yet. Once the signal `valid` and `data` from the input buffer becomes ready, the FSM in port 1 can then write the data into SRAM. At the same time the FSM in port 0 will generate the next address and repeat the process until everything is done.

When slot 1 finishes, it will notify the sequencer that every operation in this slot has finished. The sequencer completes the waiting states and enter the next instruction.

Usually, we don't do anything after the data loading is done, a `halt` instruction is automatically added to the end of the code segment. The `halt` instruction will notify the global controller that the code segment has finished. The global controller can then issue the next code segment to process the data.

## Simplified Deterministic Protocol

In some situation, the algorithm is completely static. It's more beneficial to assume the data is always ready at the next cycle or the data is always ready after some fixed delay. In this case, the data loading and storing can happen at the same time when computation is happening. We don't have to load data to the SRAM and notify the global controller first then start the computation.

The assumption of having fixed delay for reading and storing data from/to external buffer has to be guaranteed by the application-level synthesis tool. If all other algorithms in the same application are also static, there will be no problem. However, if there are dynamic algorithms in the same application, special communication barriers has to be inserted between the static and dynamic algorithms to ensure that the static algorithms can still access data whenever they need. That means, we have to insert enough buffers that can hold the complete data token or even use ping-pong buffer to ensure the data is always ready.

Till now, we are only dealing with static algorithms. therefore, we use the simplified deterministic protocol to communicate with the input/output buffer. When implementing the communication protocol, we can assume the data is always ready at the next cycle. The communication protocol is simplified to the following steps:

```
rop <read_to_input> (slot=1, port=0){
    dsu (slot=1, port=0, addr_sd=0, addr=0)
    rep (slot=1, port=0, step=1, iter=4, delay=t1)
}
rop <write_to_iosram> (slot=1, port=2){
    dsu (slot=1, port=2, addr_sd=0, addr=3)
    rep (slot=1, port=2, step=1, iter=4, delay=t1)
}
```

We also need to define the constraint to activate the two instructions at the same time:

```
linear read_to_input == write_to_iosram
```

Readers can go to the [Tutorial DRRA](../Tutorials/Tutorial_DRRA.md) to see how to use the simplified deterministic protocol to communicate with the input/output buffer.
