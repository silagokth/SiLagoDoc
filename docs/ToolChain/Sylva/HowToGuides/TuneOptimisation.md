# Tune the optimisation

Two input files steer the synthesis: **global constraints** define the feasible
design space (hard limits), and **hyper-parameters** guide the optimisation
objectives (soft preferences). See the [Sylva Input Format](../Reference/Input.md)
for the exact fields.

## Global constraints (`global_constraint.json`)

Hard limits that every generated solution must satisfy:

| Field | Limits |
| --- | --- |
| `maxWidth`, `maxHeight` | Maximum logic area (design width × height). |
| `maxLatency` | Maximum end-to-end computation latency. |
| `maxPeriod` | Required sampling period (no AlImp may exceed it). |
| `maxEnergy` | Maximum total energy. |

If the problem is infeasible, relax these limits first.

## Hyper-parameters (`hyper_parameter.json`)

Soft knobs that shape the trade-offs:

| Field | Effect |
| --- | --- |
| `bindWArea` | Weight on total area in the [binding](../Explanations/DSE-Bind.md) objective. |
| `bindWEnergy` | Weight on total energy in binding. |
| `bindWLatency` | Weight on latency preference in binding. |
| `bindRelaxationFactor` | How far above the optimal binding cost a solution may be and still be kept (e.g. `1.1` = within 10 %); enables keeping several candidates. |
| `placeRelaxationFactor` | Extra whitespace reserved during [placement](../Explanations/DSE-Place.md); larger values give more routing room (and potentially more area). |
| `placeReservedRoutingSize` | Routing area reserved around each AlImp. |

### Choosing the binding weights

The binding objective is a weighted sum of area and energy
(`bindWArea·area + bindWEnergy·energy`). Increase a weight to bias the solver
toward minimising that quantity. For example, raise `bindWArea` to favour smaller
designs, or `bindWEnergy` to favour lower-energy implementations.

### Placement: area vs. routing distance

Placement minimises a weighted combination of occupied area and estimated
communication distance. Prioritising routing distance too heavily tends to inflate
area for only a small routing gain; a balanced setting (area weighted moderately
above distance) is usually a good practical trade-off. Increase
`placeRelaxationFactor`/`placeReservedRoutingSize` if routing fails to find feasible
paths.

## Workflow

1. Start from a generated example's files.
2. Adjust constraints until the problem is feasible.
3. Sweep the weights/relaxation factors, re-running `sv-dse`, and compare the
   reported area, latency and routed distance (see
   [Read the outputs](InterpretOutputs.md)).

!!! note
    Hyper-parameters currently affect the binding and placement stages. The set of
    tunable parameters is evolving and may change in future versions.
