# Describe your own application

An application is described to Sylva as a **Homogeneous Synchronous Dataflow
(HSDF)** graph in `app_graph.json`. Each node is one algorithm; each edge is a
one-to-one data dependency between a producer port and a consumer port. Under HSDF
every node executes exactly once per iteration and produces/consumes a single
token type. The full schema is in the [Sylva Input Format](../Reference/Input.md).

## Minimal structure

```json
{
  "nodes": [
    {
      "id": "A",
      "func": "FA",
      "outputPorts": [{ "id": "A:ab", "tokenSize": 16 }]
    },
    {
      "id": "B",
      "func": "FB",
      "inputPorts": [{ "id": "B:ab", "tokenSize": 16 }]
    }
  ],
  "edges": [
    {
      "id": "A_B",
      "sourceNode": "A",
      "targetNode": "B",
      "sourcePort": "A:ab",
      "targetPort": "B:ab",
      "tokenSize": 16
    }
  ]
}
```

## Rules to follow

- **`id`** uniquely identifies a node *instance* in the graph.
- **`func`** links a node to its implementations in the AlImp library
  (`alimp_lib.json`): there must be a library entry whose function matches `func`.
- Every edge must reference a real `sourcePort` on its `sourceNode` and a real
  `targetPort` on its `targetNode`, and **both ports must agree on `tokenSize`**.
- If one node feeds several consumers, add **one edge per consumer**.
- Keep the graph HSDF: a directed acyclic graph where each node fires once per
  iteration. Functional-level parallelism (reusing a block over time) is not yet
  supported.

## Tips

- Start from a generated example (`./bin/config --name minimum --output config`)
  and edit `app_graph.json` to match your graph; this guarantees a consistent set
  of the other four input files to begin from.
- Make sure every `func` you use has at least one matching entry in your
  [AlImp library](AlimpLibrary.md).
