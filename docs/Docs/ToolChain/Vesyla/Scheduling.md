# Scheduling

## Problem Defination

In Vesyla, the generated instructions have fairly long life-time and are highly cooperative. Which means they are not just single-cycle actors with simple precedency relation that fight for resources. Instructions running in DRRA microthreads might actively coordinate themselves with other instructions executing in some other microthreads. Cooperation with other instructions can happen at any time when an instruction is alive and it can happen multiple times with multiple different other instructions. Further more, the life-time of an instruction in Vesyla can be undetermined and depend on other instructions in terms of both precedency relation and resource availability.

To simplify the scenario a little bit, we consider the **critical points** of those instructions as the atomic unit objects (jobs/actors/operations) that need to be scheduled in the cooperative instruction scheduling problem which will be defined later. What are the critical points? They can be starting and ending point of instructions. They can be the time points when a instruction change its resource usage. They can be the time points when a instruction need to coordinate with other instructions. In short, whenever an instruction changes anything, it's a critical point. Critical points don't have execution duration, it just represents a timestamp. However, critical points can have resoure usage attached to it. Resource usage requires time duration.

With the usage of critical points, a new phenominal appears. Some instructions can have undetermined life-time before scheduling. When those instructions are broken down into critical points, the resource usage of those critical points need special treatment since their usage duration are undetermined. We need to assign a **LOCK** resource usage frame to the critical point starting to use the resource and a **KEY** resource usage frame to the critical point finishing to use the resource. The resource between **LOCK** and **KEY** frame during scheding should be marked **unavailable** to other critical points.

Cooperative instruction scheduling problem thus is very different from the classic instruction scheduling problem. In classic instruction scheduling problem, either positive or negative time-lag exists between a pair of instructions. While between a cooperative critical points pair, both minimal and maximum time-lag may exist, which constrants the precedence relation to a possibly closed time frame. A special case is when minimal and maximum time-lag are equal, representing the two critical points will have an exact time difference. The introduce of **LOCK** and **KEY** frame for resource usage is also very different from conventional instruction scheduling problem.

Now, we formally define the scheduling model and the scheduling problem.

!!! Warning
	MODEL AND PROBLEM DEFINATION

## Problem Simplification

The scheduling model defined above can not be used directly because it's high complexity creates a vast solution space and scheduling algorithm will have hard time to navigate to the correct direction.

Therefore, we propose one assumption and four simplification step to simplify the model. The simplification processes transform the model to equivalent but simpler model hence shrink the solution space.

### Delay bound assumption

Delay bounds of an edge in depenency graph can be either negative or positive as long as the higher bound $d_h$ is not less than lower bound $d_l$. An edge with $d_l=-\infty$ or with $d_l\le d_h \lt 0$ can be easily converted to an edge with $d_l\neq -\infty$ and $d_h\ge 0$. Therefore, we assume that: every edge in dependency graph will have a delay bounds satisfying $d_l\neq -\infty$, $d_h\ge 0$ and $d_l\le d_h$.

### Parking Hard-links
Hard-links are edges with constant delay ($d_l=d_h$). For example, $A\xrightarrow[]{\text{[w,w]}}B$ is a hard-link, it describs the constraint that the schedule time of B is exactly $w$ cycles after the schedule time of A.

All vertices linked by hard-links should be scheduled together, because if any vertex has been scheduled, the schedule time of other vertices which directly or indirectly linked to the scheduled vertex can be determined immediately. Therefore, packing hard-linked vertices together can reduce the size of graph and accelerate the scheduling process.

Pseudo Algorithm is shown below:

```` C++
Graph packing_hard_links(Graph g){
	Graph g1 = remove_soft_links(g);
	Component C = find_connected_components(g1);
	Graph g2;
	for(auto c : C){
		vector<Vertex, int> offset_map = find_offset_for_each_vertex(c);
		Vertex vc;
		Graph g3;
		for(auto v : c.vertices()){
			v.schedule_time = offset_map(v);
			g3.add_vertex(v);
		}
		vc.add_child(g3)
		g2.add_vertex(vc);
	}
	for(auto e: edges(g)){
		if(is_soft_link(e) && in_different_component(e.src, e.dest)){
			Edge e1 = reshape_edge_accroding_to_g2(e);
			g2.add_edge(e1);
		}
	}
	return g2;
}
````

### Remove Redundant Links


Pseudo Algorithm is shown below:

## Solution Space Exploration



