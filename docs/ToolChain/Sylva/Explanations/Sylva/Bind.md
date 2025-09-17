# Binding Process

The binding process select the one of the alimp from the alimp library for each node in the app graph. The binding process has to consider all the global constraints. Since at this stage, there is little detail that has been synthesized, the approximation of the solution cost will be crude when evaluate the design point. Therefore, the binding process actually try to keep several good candidates instead of a single one.

The binding process will be done in two steps -- optimal binding and approximate optimal binding.

## Optimal Binding

This process formulates the binding problem and minimizes the total cost function. It returns a single solution with the minimal cost.

### Original Decision Variables

`select_alimp_{NODE_ID}` is an integer variable that determines which alimp (in term of index in the library) is selected for each node in the app graph. The number of this type of variable is equal to the number of nodes in the app graph. Each variable is an integer variable with a range from 1 to the number of alimp in the library.

### Derived Decision Variables and constraints

`area_nodes` is a list of area for all selected nodes in the app graph, and `energy_nodes` is a list of energy for all selected nodes in the app graph. Since each node can have multiple alimp options to choose from, the above two variables make use of `select_alimp_{NODE_ID}` to get the targeting alimp information of a node.

`width_{NODE_ID}` is a list of alimp width for this node, `height_{NODE_ID}` is a list of alimp height for this node, and the same goes for `energy_{NODE_ID}`. Each node has its own set of these variables. For geometric constraints, we also have to check the following conditions to ensure that the binding alimp size is not bigger than the size of the global constraints:

- `width_{NODE_ID}[selected_alimp_{NODE_ID}] <= global_constraint.max_width`
- `height_{NODE_ID}[selected_alimp_{NODE_ID}] <= global_constraint.max_height`

Where MAX_WIDTH and MAX_HEIGHT are global constrainsts imposed the user.

To calculate the area and enery for each node, we post the following constraints, respectively:

- `width_{NODE_ID}[selected_alimp_{NODE_ID}] * height_{NODE_ID}[selected_alimp_{NODE_ID}]` == `area_nodes[i]`
- `energy_{NODE_ID}[selected_alimp_{NODE_ID}] == energy_nodes[i]`

Where i is an index associated with the `NODE_ID`.

Now we can post the global constraints for geometric and energy constraints:

- sum(`area_nodes) <= global_constraint.max_width * global_constraint.max_height`
- sum(`energy_nodes) <= global_constraint.max_energy`


The above constraints deal with the geometry and energy consumption. Next, we need to deal with the latency and sampling frequency. First, we need to create some variables to represent the execution timing points.

`latency_nodes` is a list of execution times of every node in the app graph. We get this value by linking the chosen alimp latency field to it:
`start_time_nodes` is a list of start times of every node in the app graph. The start time of a node is the time when the node starts to execute.
`end_time_nodes` is a list of end times of every node in the app graph. The end time of a node is the time when the node ends to execute.

First, we link the selected alimp latency to `latency_nodes`:
- `latency_{NODE_ID}[selected_alimp_{NODE_ID}] == latency_nodes[i]`

Naturally, we have the following constraints:
- `start_time_nodes[i] + latency_nodes[i] == end_time_nodes[i]` for all nodes in the app graph.

We also introduce a half-latency concept to set the minimal starting point of the successor nodes assuming the maximum overlapping period is 50% of the latency. The half-latency constraint is calculated as follows:

- `start_time_nodes[j] >= start_time_nodes[i] + (latency_nodes[i] div 2)` ffor all edges (i, j) in the app graph where j is a successor node of node i.

If a node does not have predecessors, then the start time of the node is 0. We need to post the following constraints:

- `start_time_nodes[i] == 0` for all nodes in the app graph that do not have predecessors.

Finally, we need to post the following constraints to ensure that the all the nodes finish before the global max latency:

- `end_time_nodes[i] <= global_constraint.max_latency`

Now, considering the sampling period. We need to make sure that no alimp has a bigger latency than the entire sampling period. We need to post the following constraints:

- `latency_nodes[i] <= global_constraint.max_period` for all nodes in the app graph.

### Cost Function

We convert a multi-objective problem to a single objective by transforming it to a linear combination of the area and energy. The cost function is defined as follows:

```
objective == hyper_parameter.bind_w_area * sum(area_nodes) + hyper_parameter.bind_w_energy * sum(energy_nodes)
```

## Approximate Optimal Binding

The approximate optimal binding process is exactly the same as the optimal binding process, except that we post an additional constraint to limit the total objective function cost to a scaled version of the minimal cost found by the optimal binding process. This is done by adding the following constraint:

```
objective <= hyper_parameter.bind_relaxation_factor * objective_min
```

We also make the problem a satisfaction problem instead of minimization problem. It means that we will obtain a list of solutions that satisfy the above constraints. The solutions are not guaranteed to be optimal, but they are guaranteed to be within a certain range of the optimal solution. We then order the solutions by the cost function and return the top N solutions. The N is also a hyper parameter that can be set by the user.
