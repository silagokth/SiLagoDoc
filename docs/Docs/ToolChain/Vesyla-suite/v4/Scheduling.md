# Instruction Scheduling

!!! Warning
    This document is still under construction. The content is not complete.

The instruction scheduler in Vesyla-suite is a stand-alone program that can generate assembly instruction list for DRRA-2 from a proto instruction list and a constraint file. The core scheduling process targets a single `epoch` region. The proto-assembly file and constraint file will be converted to an internal timing model that can work as the intermeditate representation for finally converting to constraint programing (CP) model. We use the [or-tools (python binding)](https://developers.google.com/optimization) from Google to finally solve the scheduling problem once the CP model is created.

## Timing Model

The timing model consists two parts:

1. Operation behavior description
2. Timing constraints

Operation behavior description models the timing behavior of every resource operation (rop) and control operation (cop). It uses concepts like **operation**, **event**, and **anchor** similar to the proto-assembly language.

Each operation will have one or more events, they are named numerically as `e0`, `e1`, etc. Among events, it is possible to apply two types of transformation operator: transition (**T** operator) and repetition (**R** operator). The **T** operator describes the timing transition from one event to another event. While the **R** operator describes the repetition of a single event or a group of events. Those operator can be applied recursively to form very complex timing behavior.

!!! example
    ```
    operation a T<6>(R<2,3>(T<4>(e0, e1)), e2) 
    ```
    The above expression describe an operation *a*, it has three events: *e0*, *e1*, and *e2*.

    The expression `T<4>(e0, e1)` means that four cycles after the event *e0*, *e1* happens.

    The expression `R<2,3>(T<4>(e0, e1))` means that the above transition is repeated twice and the repetitions are separated by 3 cycles.

    Finally, the whole expression `T<6>(R<2,3>(T<4>(e0, e1)), e2)` means that after the above repetition and further delay of 6 cycles, the event *e2* will happen.

The timing constraint used by the is the model is the same as the constraint file. It's a direction translation.

## CP Model

For each operation, event, and anchor, we create a variable in the CP model.

The event and anchor variables are restricted by the corresponding operation variables according to the operation timing model. Those restrictions are translated to the CP model as constraints.

Custom constraints are automatically added to the CP model according to the constraint file.

The objective function is to minimize the total time of the epoch.
