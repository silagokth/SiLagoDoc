# Placement Process

The placement process takes one of the binding solution and try to place the alimps in a 2D grid. It has to consider all the global constraints, try to reserve some area for routing, and try to minimize the communication cost. Similar to the binding process, the placement process is done in two steps -- optimal placement and approximate optimal placement.

## Preprocessing

Before starting the placement, we artificially enlarge the alimp area by a factor of `enlarge_factor` to reserve some area for routing. Currently, the factor is 1 unit for width and 1 unit for height, and these numbers grow when there exists more than one input and output port.

## Optimal Placement

### Original Decision Variables

`x` and `y` are the original decision variables that contain the starting coordinates of the alimps. Each node has its own `x` and `y` positions. Additionally, we have a variable named `N` to represent the total number of nodes in this application.

### Derived Decision Variables and Constraints

First we set the maximum width and height, and we create a set of constraints to make sure that 1) each node fits inside the allowed area, and 2) there is no overlapping between any two nodes, which form an optimization problem of bin-packing.

```
MAX_WIDTH = global_constraint.max_width;
MAX_HEIGHT = global_constraint.max_height;

constraint forall(i in 1..N)(
    x[i] + widths[i] <= MAX_WIDTH /\
    y[i] + heights[i] <= MAX_HEIGHT
);

constraint forall(i, j in 1..N where i < j)(
  (x[i] + widths[i] <= x[j] \/ x[j] + widths[j] <= x[i]) \/
  (y[i] + heights[i] <= y[j] \/ y[j] + heights[j] <= y[i])
);
```

Where widths and heights are lists of selected alimp for each node with respect to index i/j.

Then, we define the `max_x_position` and `max_y_position` decision variables to represent the maximum x and y coordinates of the alimps by posting the following constraints:

```
constraint forall(i in 1..N)(
  x[i] + widths[i] <= max_x_position /\
  y[i] + heights[i] <= max_y_position
);
```

We also force the floor plan after placement to be a more square-like shape. We require that the width cannot be larger than twice the height and vice versa. This is done by posting the following constraints:

```
constraint max_x_position * 2 >= max_y_position;
constraint max_x_position <= max_y_position * 2;
```

We would like also to fix the first node to be placed at the bottom-left corner. This requires the following constraints:

```
constraint x[1] < ((MAX_WIDTH + 1) div 2);
constraint y[1] < ((MAX_HEIGHT + 1) div 2);
```

The total area should be less than the max_area specified by the global constraints. This is done by posting the following constraint:

```
constraint max_x_position * max_y_position <= MAX_WIDTH * MAX_HEIGHT;
```

### Objective Function

The objective function is to minimize the total area of the floor plan. This is done by posting the following constraint:

```
solve minimize (max_x_position * max_y_position);
```

## Approximate Optimal Placement

The approximate optimal placement takes the `max_x_position` and `max_y_position` decision variables from the optimal placement and relaxes the constraints by extending the maximum width and height variables as follows

```
MAX_WIDTH <= old_max_x_position * relaxation_factor
MAX_HEIGHT <= old_max_y_position * relaxation_factor
```

We then post all the constraints similar to the optimal placement process with some additional constraints described below, but there is an additional consideration point that we need to evaluate to be able to factor in the cost of wire length.

First, we calculate x and y positions for each edge source and target. We then compute the distance matrix between all the connected alimps by computing their manhattan distances of their input/output port positions. Lastly, we compute the weighted distance matrix by multiplying the distance matrix with the connectivity factor of each connection.

```
constraint forall(i in 1..E)(
  dx[i] = abs(source_x[i] - target_x[i]) /\
  dy[i] = abs(source_y[i] - target_y[i]) /\
  distance_matrix[i] = dx[i] + dy[i] /\
  weighted_distance[i] = edge_conns[i] * distance_matrix[i]
);
```

### Objective Function

Then, we minimize the total weighted distance of all the connections and also the total area by posting the following constraint:

```
solve minimize (2 * sum(weighted_distance) + (max_x_position * max_y_position));
```
