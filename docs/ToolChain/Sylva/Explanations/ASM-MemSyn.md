# Memory Synthesis

## Main concept 

From the previous steps, the tool models the schedule and estimated memory characteristics of each edge. Here what this step needs to address is that A) it needs to assign the type of memories inuse, B) it precisely assigns the positions for output buffers, transporters, and input buffers, C) it addresses how many communication channels needed to transfer data from one section of the output to the corresponding section of the input, D) in those communication channels, it assigns the time slot for each token to travel on one of the channels.

Running the memory synthesis can also be beneficial in a way that it can leverage many valid solutions and return the most cost-effective one from that list. In the below figure, for example, it presents two possible solutions for the given requirement. One thing that cannot be touched is the Alimp library where it tells from which channel of the output Alimp a token is sent and to which channel of the input Alimp that token is received, along with the timestamps for both input and output. Here, we can see that three output and input channels need to be connected the output/input buffers. However, what can be manipulated and determined to find a good solution is that how we partition the memory and which type of memory we use. 

In this example, the one-bank solution uses one giant memory for both input and output where the output memory needs to be a 3x1 register file, and vice versa for the input memory. For this to work, we may only need one communication channel (one transporter) to connect from output to input. Assuming that there is another valid solution where we can place three 1x1 fifos to both output and input buffer layers, with three transporters and channels to connect them. To evaluate which design is better, it requires a cost function which factors in the cost of memory, type of memory, size of memory, and cost of communication channels. Without this cost function, we cannot really tell whether the one-bank solution or the three-bank solution is better, which is essentially the task of memory synthesis. 

![Banking Example](ASM-MemSyn/banking_example.png)

### Goals

Next, we would like to tell what we exactly know and what needs to be determined in this step. In the GLIC synthesis, a set of information about the patterns and memory has been pre-determined because in that step, we assumed that the memory for both sides is one big register file with infinite number of input and output ports, similar to the one-bank solution above. This is however unrealistic because such imaginary register file is unimplementable, or is but with great cost. So, the following are fixed/estimated information that we obtained from GLIC synthesis.

1. Start times (fire times) of every node - These variables are fixed.
2. Output and Input patterns of each edge - since the fire times are known and patterns are defined by an HLS library, the output and input patterns (address, time, and channel) can be calculated as fixed integers. In the example below, these patterns are $\Patterns_{A}$ and $\Patterns_{B}$ 
3. The estimated number of communication channels for connecting A and B.
4. The esttimated buffer sizes of the input and output buffers.

The following are the questions that need to be answered using the above information as hard and soft constraints - hard constraints are the patterns and soft constraints being the estimated buffer sizes, etc.

1. Partition settings - If segmenting a big connection problem into many smaller problems is possible, we always want to do as the implemention cost is likely lower.
2. Memory types - For each partition, the output and input memories are required to be assigned the memory type, more details about this later.
3. Exact locations - For all three components - output buffers, input buffers, and transporters, they are assigned the positions, where all of them consume one DRRA cell height, a transporter has a width of one DRRA cell, and buffers have a width of a multiple of one DRRA cell.
4. Memory sizes - Finding which size is suitable for each memory. Previously, the estimated sizes were assigned as any integer, but this cannot be put into practice as the memory size should be a multiple of two. Here, the problem works out which valid memory size of each memory should be.
5. Transport patterns - For each partition, there could be a case where it uses more than one communication channel. So, it is necessary to know that which communication channel a token uses, at which time this token is sent out from the output buffer, and also from which/to which channel this token is transferred.

![Problem statement](ASM-MemSyn/problem_statement.png)


### Memory type

Now, we have three memory models to mimic the digital hardware, each has its own properties as following 

- FIFO: one input port, one output port.
- RAM: one/two input ports, one/two output ports. 
- Register file: infinite input ports, infinite output ports.

We assume that there's no upper limit of how large a memory could become, but it must has the size of a multiple of two. Of course, this does not sound realistic. We'll add limitations later. 

### Paritition

To find out the smallest partitions, a parition has to satisfy these rules - 1) all data tokens from all input channels of a partition can be transported to the input channels inside this partition only, 2) input/output channels cannot overlap between partitions, e.g., partitioning a list of output channels as [0, 1, 3], [2, 4, 5] would be incorrect because output channel 2 overlaps the first partition, so they need to merge into one parition. 

All possible partition settings of a set of smallest partitions {A,B,C,D} would be below list

```
[[A,B,C,D], [AB,C,D], [A,BC,D], [A,B,CD], [ABC,D], [A,BCD], [ABCD]]
```



## Solving the problem

### Configuration problem

Since the problem has so many variables and some of them even have so many constraints to follow, such as setting the memory type to fifo has to include the constraints to model the fifo behaviour, this pushes the constraint solver to the point where it cannot solve the problem in one go. So, instead of finding out all answer in one problem, we think of the big problem as small problems that answer only one or two questions at a time, and do this in a nested loop.

This is how we break down the big problem:

1. Select only a certain number of partition settings and loop over to find out which partition setting gives the lowest cost.
2. For each setting, loop over every partition in the setting.
3. For each parition, it is now possible to define a set of memory type for a pair of input and output buffers. Then loop over every hardware configuration to find the lowest hardware setting. 
4. Solve the problem to get the cost of the current setting.

```
partition_best_cost = [];
for partition_setting in all_possible_partitions {
	segment_best_cost = [];
	for partition in partition_setting {
		best_cost = MAX;
		for config in configs {
			cost = solving(current); 
			if cost < best_cost {
				best_cost = cost;
			} 
		}
		segment_best_cost.add(best_cost);
	}
	partition_best_cost.add(segment_best_cost);
}			
```


After getting the result, we can easily work out which partition setting and memory types of the input and output buffers we need to use. Then, we solve the problem again but this time for the setting that gives the lowest cost result, and hence, we solve the problem. 


### Scheduling problem

This section deals with, given the settings and hard constraints, how do we come up with the answers to the transport patterns, memory sizes, and exact locations. 