# Binding Process

The binding process select the one of the alimp from the alimp library for each node in the app graph. The binding process has to consider all the global constraints. Since at this stage, there is little detail that has been synthesized, the approximation of the solution cost will be crude when evaluate the design point. Therefore, the binding process actually try to keep several good candidates instead of a single one.

The binding process will be done in two steps -- optimal binding and approximate optimal binding.

## Optimal Binding

This process formulates the binding problem and minimizes the total cost function. It returns a single solution with the minimal cost.

### Original Decision Variables

`BINDING_VARS` is a list of variables that determines which alimp (in term of index in the library) is selected for each node in the app graph. The size of the list is equal to the number of nodes in the app graph. Each variable is an integer variable with a range from 0 to the number of alimp in the library minus 1.

### Derived Decision Variables and constraints

`BINDING_VECS` is a binary expand version of `BINDING_VARS`. If there is a node `i` in the app graph, and the selected alimp is `j`, then `BINDING_VECS[i][j]` is 1, and all other `BINDING_VECS[i][k]` are 0. To achieve this, we need to post the following constraints:

- The universe of all variable in `BINDING_VECS` is {0, 1}.
- The sum of `BINDING_VECS[i][k]` for all `k` is 1.
- `BINDING_VARS[i]==x` is enforced only if `BINDING_VECS[i][x]=1`.

`ALIMP_AREA` is a list of area of node in the app graph. To calculate the area for each node, we post the following constraints:

- `ALIMP_AREA[i]==ALIMP_LIBRARY[i][x].height * ALIMP_LIBRARY[i][x].width` is enforced only if `BINDING_VARS[i]==x`.

`ALIMP_ENERGY` is a list of energy of node in the app graph. To calculate the energy for each node, we post the following constraints:

- `ALIMP_ENERGY[i]==ALIMP_LIBRARY[i][x].energy` is enforced only if `BINDING_VARS[i]==x`.

We also need to post the following constraints to ensure that the binding alimp size is not bigger than the size of the global constraints:

- `ALIMP_LIBRARY[i][x].height <= GLOBAL_CONSTRAINTS.max_height` is enforced only if `BINDING_VARS[i]==x`.
- `ALIMP_LIBRARY[i][x].width <= GLOBAL_CONSTRAINTS.max_width` is enforced only if `BINDING_VARS[i]==x`.
- `ALIMP_LIBRARY[i][x].energy <= GLOBAL_CONSTRAINTS.max_energy` is enforced only if `BINDING_VARS[i]==x`.
- `ALIMP_LIBRARY[i][x].height * ALIMP_LIBRARY[i][x].width <= GLOBAL_CONSTRAINTS.max_area` is enforced only if `BINDING_VARS[i]==x`.
- `sum(ALIMP_AREA) <= GLOBAL_CONSTRAINTS.max_area`.
- `sum(ALIMP_ENERGY) <= GLOBAL_CONSTRAINTS.max_energy`.

The above constraints deal with the geometry and energy consumption. Next, we need to deal with the latency and sampling frequency. First, we need to create some variables to represent the execution timing points.

`START_TIME` is a list of start times of every node in the app graph. The start time of a node is the time when the node starts to execute.
`END_TIME` is a list of end times of every node in the app graph. The end time of a node is the time when the node ends to execute.
`LATENCY` is a list of execution times of every node in the app graph. We get this value by linking the chosen alimp latency field to it:

- `LATENCY[i] = ALIMP_LIBRARY[i][x].latency` is enforced only if `BINDING_VARS[i]==x`.

Naturally, we have the following constraints:

- `START_TIME[i] + LATENCY[i] == END_TIME[i]` for all nodes in the app graph.

We also introduce a variable `HALF_LATENCY` to represent the half of the latency of each node. The half latency is used to set the minimal starting point of the successor nodes assuming the maximum overlapping period is 50% of the latency. The half latency is calculated as follows:

- `HALF_LATENCY[i] = LATENCY[i] / 2` for all nodes in the app graph.

Then, we have the following constraints concerning the execution order:

- `START_TIME[i] + HALF_LATENCY[i] == START_TIME[j]` for all edges (i, j) in the app graph.

If a node does not have predecessors, then the start time of the node is 0. We need to post the following constraints:

- `START_TIME[i] == 0` for all nodes in the app graph that do not have predecessors.

Finally, we need to post the following constraints to ensure that the all the nodes finish before the global max latency:

- `END_TIME[i] <= GLOBAL_CONSTRAINTS.max_latency`

Now, considering the sampling period. We need to make sure that no alimp has a bigger latency than the entire sampling period. We need to post the following constraints:

- `LATENCY[i] <= GLOBAL_CONSTRAINTS.max_period` for all nodes in the app graph.

### Cost Function

We convert a multi-objective problem to a single objective by transforming it to a linear combination of the area and energy. The cost function is defined as follows:

```
obj == HYPER_PARAM.area_weight * sum(ALIMP_AREA) + HYPER_PARAM.energy_weight * sum(ALIMP_ENERGY)
```

## Approximate Optimal Binding

The approximate optimal binding process is exactly the same as the optimal binding process, except that we post an additional constraint to limit the total objective function cost to a scaled version of the minimal cost found by the optimal binding process. This is done by adding the following constraint:

```
obj <= HYPER_PARAM.bind_relaxation_factor * obj_min
```

We also make the problem a satisfaction problem instead of minimization problem. It means that we will obtain a list of solutions that satisfy the above constraints. The solutions are not guaranteed to be optimal, but they are guaranteed to be within a certain range of the optimal solution. We then order the solutions by the cost function and return the top N solutions. The N is also a hyper parameter that can be set by the user.
