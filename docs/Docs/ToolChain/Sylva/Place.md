# Placement Process

The placement process takes one of the binding solution and try to place the alimps in a 2D grid. The placement process has to consider all the global constraints, try to reserve some area for routing and try to minimize the communication cost. Similar to the binding process, the placement process is done in two steps -- optimal placement and approximate optimal placement.

## Preprocessing

Before starting the placement, we artificially enlarge the alimp area by a factor of `enlarge_factor` to reserve some area for routing. Currently, the factor is 1 unit for width and 1 unit for height.

## Optimal Placement

### Original Decision Variables

`x_start` and `y_start` are the original decision variables that contain the starting coordinates of the alimps.

### Derived Decision Variables and Constraints

`x_end` and `y_end` are the derived decision variables that contain the ending coordinates of the alimps. We enforce the constraints to link the `x_start` and `y_start` to the `x_end` and `y_end` decision variables.

We create interval variables `x_intervals` and `y_intervals` to package the start, end, and size of the alimps into a single variable that can be used in `NoOverlap2D` constraints:

```
NoOverlap2D(x_intervals, y_intervals)
```

Then, we define the `max_x_position` and `max_y_position` decision variables to represent the maximum x and y coordinates of the alimps by posting the following constraints:

```
max_x_position = max(x_end)
max_y_position = max(y_end)
```

We also force the floor plan after placement to be a more square-like shape. We require that the width cannot be larger than twice the height and vice versa. This is done by posting the following constraints:

```
max_x_position <= 2 * max_y_position
max_y_position <= 2 * max_x_position
```

The total area should be less than the max_area specified by the global constraints. This is done by posting the following constraint:

```
total_area == max_x_position * max_y_position
total_area <= max_area
```

### Objective Function

The objective function is to minimize the `total_area` of the floor plan. This is done by posting the following constraint:

```
minimize total_area
```

## Approximate Optimal Placement

The approximate optimal placement takes the `max_x_position` and `max_y_position` decision variables from the optimal placement and relax the constraints by multiplying the `max_x_position` and `max_y_position` decision variables by a relaxation factor:

```
max_x_position <= old_max_x_position * relaxation_factor
max_y_position <= old_max_y_position * relaxation_factor
```

We then post all the constraints similar to the optimal placement process with some additional constraints described below.

First, we compute the distance matrix between all the connected alimps by computing their manhattan distance of their input/output port position.

```
d_i_j == abs(x_i_out_port - x_j_in_port) + abs(y_i_out_port - y_j_in_port)
```

Then we compute the weighted distance matrix by multiplying the distance matrix with the connectivity factor of each connection.

```
w_d_i_j == d_i_j * connectivity_factor
```

Then, we define the `total_weighted_distance` decision variable to represent the total weighted distance of all the connections by posting the following constraint:

```
total_weighted_distance = sum(w_d_i_j)
```

### Objective Function

The objective function is to minimize the `total_weighted_distance` of the floor plan. This is done by posting the following constraint:

```
minimize total_weighted_distance
```
