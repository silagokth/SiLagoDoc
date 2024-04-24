# Instruction Scheduling

!!! Warning
    This document is still under construction. The content is not complete.

The instruction scheduler in Vesyla-suite is a stand-alone program that can generate instruction list for DRRA-2 from a proto instruction list and a constraint file.

The instruction scheduler contains 3 processes:

* **generate**: Generate the operation timing model file from the proto-instruction list and the constraint file.
* **schedule**: Use the operation timing model file to schedule the individual operations and their internal delays.
* **sync**: Synchronize the scheduled operations by properly inserting WAIT instructions so that the operations starts at the correct cycle.

## Generate

The generate process reads the proto-instruction list and the constraint file. It then generates the operation timing model file.

### Proto-instruction File

The proto-instruction file contains a list of instructions just like the normal assembly file. However, it defines the operations and the instructions attached to them. See the following example:

!!! example
    ```assembly
    cell 0_0
    # load data
    operation iodsu_0_0_snd slot=1, port=0
    dsu slot=1, port=0, init_addr=0
    rep slot=1, port=0, level=0, iter=8, step=1, delay=t1
    operation iodsu_0_0_rcv slot=1, port=2
    dsu slot=1, port=2, init_addr=0
    rep slot=1, port=2, level=0, iter=8, step=1, delay=t1


    cell 1_0
    operation route_1_0 slot=0, port=2
    route slot=0, option=0, sr=1, source=1, target= 0b0000000000000100
    ```

The above example defines two instruction lists, each of which is assigned to a cell: cell[0,0] and cell[1,0].In cell[0,0], there are two operations called *iodsu_0_0_snd* and *iodsu_0_0_rcv*. You can use any normal identifier as operation name. You also need to specify the slot and port of that operation when define an operation. There can be one or more instructions attached to each operation. These instructions could be normal configuration instructions like **DSU** or **ROUTE**, or repeat instruction **REP**, or state transition instruction **FSM**. 

You should not write any **WAIT** or **ACT** instruction in the proto-instruction list. They will be automatically inserted by the scheduler during the *sync* process.

Notice that, you can also use variables to represent delay time. These variables will automatically be registered and figured out by the scheduler based on the constraint file.

### Constraint File

Constraint file records one constraint in each line. You can use any normal operators such as `+`, `-`, `*`, `==`, `!=`, `>`, `<`, `>=`, `<=`, `&&`, `||`, `!`, `()`, etc. You can also use variables that refer to operation event slices in the constraint file. The format of variable consists of three parts: `operation_name`, `event_name`, and `slices`. For example, `iodsu_0_0_snd` is an operation name, 

!!! example
    If we have the following proto-instruction file:
    ```
    cell 0_0
    operation dsu_0_0 slot=0, port=0
    dsu slot=0, port=0, init_addr=0
    rep slot=0, port=0, level=0, iter=8, step=1, delay=t1
    operation dpu_0_0 slot=1, port=0
    fsm slot=1, port=0, delay_0=t1, delay_1=t2, delay_2=t3
    rep slot=1, port=0, level=0, iter=2, step=1, delay=t4
    rep slot=1, port=0, level=1, iter=4, step=1, delay=t5
    ```

    Then, we can use constraints such as:
    ```
    dsu_0_0 == dpu_0_0 + 1
    dsu_0_0.e0 == dpu_0_0.e0 + 1
    dsu_0_0.e0[0][1] >= dpu_0_0.e0[0][1] - 1
    ```

    Each constraint must be a comparison expression. Each variable in the expression must be pre-existed. They are usually a slice of a specific event. For example, ``dsu_0_0.e1[0][0]`` means the 0th iteration of level-1 loop and the 1th iteration of the level-0 loop of the event e0 in the operation dsu_0_0. The event name are implied when you write the instructions of each operation. By default, there is only 1 event: e0. With **FSM** instruction, you can create more events: e1, e2, or even e3. The **REP** instruction will not create new events, but it will affects how many level of slice you should use. If the event name is omitted, we assume it's e0. If the slice is omitted, we assume they are all 0s.

### Operation Timing Model File

The timing model file is the output of the generate process. It contains three parts:

* **Operation List**: The operation definition list. Each operation has a unique name and a construction of transition and repetition operators. The events for the operation is also generated there.
* **Anchor List**: Anchors are the event slice used in the constraint file. Since event slice name is not a valid identifier, we need to define it as a anchor to make it a valid identifier. Then it can be used to define the actual constraints which can be directly processed by the CP solver.
* **Constraint List**: The constraints are generated from the constraint file. The event slice name has been substituted. The constraints are processed by the CP solver to figure out the delay time for each operation.

