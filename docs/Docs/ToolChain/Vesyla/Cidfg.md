!!! warning
    Documentation is not complete!

# CIDFG - Control Index Data-Flow Graph

## Vertices

### Indexing Vertex

An indexing vertex is a vertex that recieves address constraints (scalar tokens) as inputs and generates
address sequence (vector token) as outputs. Indexing vertex has an address generation function which
takes address constraints as input and generate address sequence. A tipical address
generation function are affine function $y = ax + b$ in which $a$ and $b$ are address
constraints, $x$ is an implied sequence [0, 1, 2, ..., N] ($N$ is also an address constraint)
and $y$ is the generated output sequence.

Address constraint can be either a constant number or a scalar variable. If all the address
constraint inputs of an indexing vertex are constant numbers, the indexing vertex
is **static**; if any of its inputs are variable and origin of those variable are
linked to either constants or static loop iterators, the indexing vertex is then
**parametrically static** because it can be speculated at compile time; If the
variables are somehow linked to any run-time calculated variable, the indexing vertex
will become **dynamic** whoes outputs cannot be determined at compile time.
