# Vesyla Programming Guide (v3)

!!! note
    This page is written for vesyla-suite **version 3**. For vesyla-suite version 2, please see [Vesyla Programming Guide v2](../../v2/VesylaProgrammingGuide/).

!!! note
    Vesyla-suite version 3 is under active development. Some of the features that exists on version 2 may not be available on version 3. We will update this page actively to reflect the changes.

## Basics

### General Guide

Vesyla accept modified C++ code as input language. You shouldn't write the C++ code like a programming language. You should instead use it as a tool to model the behaviour of the hardware. Vesyla supports small portion of C++ grammar. There are some generic rules expressing the programming style vesyla accepts.

* General function call is not allowed unless the function is predefined as primitive function.
* Variables except for constant variable and loop iterator should always model real memory components, and should be declared with implicit or explicit pragma.
* Supported constant numbers are integers.
* For-loop is supported. If-statement is largely supported. While-loop is not supported.

### Pragma

Pragma is the notation that guides Vesyla during synthesis process. Vesyla recognize pragma starting with symbols ```#pragma```. The main function of pragmas is specify allocation and binding information since Vesyla can't perform automatic allocation and binding. Section [Variable Declaration](#variable-declaration), [Arithmetic Operation](#arithmetic-operation), [Address Constraint](#address-constraint) [DPU Chain](#dpu_chain) and [DPU Internal Scalar Register](#dpu-internal-scalar-register) describe how to use pragmas to allocate and bind resources. Some other usage of pragma also exist, check section [Resource Sharing Region](#resource-sharing-region) for more detail.

There are two types of pragmas: single-line pragma and block pragma. Single-line pragma only affects the statement immediately following it. Block pragma affects all statements within the block. Block pragma is started with ```#pragma start``` and ended with ```#pragma end```. The following example shows how to use pragmas to allocate and bind resources.

!!! Example
		
	Explicit single-line pragma that binds the variable a to register file [0,0]
	```c++
	#pragma bind rf_0_0
	RF a;
	```
		
	Implicit binding without pragma that binds the variable rf_0_1 to register file [0,1]
	```c++
	RF rf_0_1;
	```

	Block pragma that makes declare a conflict-free zone.
	```c++
	#pragma start conflict_free_zone
	...
	...
	#pragma end conflict_free_zone
	```


### Variable Declaration

#### Storage Variable

There is three type of vector storage variables: Register file variable, SRAM variable and I/O variable. Register file variable is used to model register file. SRAM variable is used to model SRAM scratch-pad memory. I/O variable is used to model input and output buffer.

!!! example
	Declare a register file variable.
	```c++
	#pragma bind rf_0_0
	RF a;
	```

	Declare a SRAM variable.
	```c++
	#pragma bind sram_0_0
	SRAM b;
	```

You should not declare any I/O variables. They are defined as global varialbes in the generated header file. You can use them as normal variables. The input buffer is named ```__input_buffer__``` and the output buffer is named ```__output_buffer__```. You can only read from input buffer and write to output buffer. Writing to input buffer and reading from output buffer are not allowed.


#### Transient Variable

Transient variables are not tight to any hardware resource. It describes the unorganized stream of data after reading from the source storage variables and before writing to the destination storage variables.

The type of transient variables depend on the supported data transfer mode on DRRA fabric. Currently, we support four types of transient variable types:

* ```STREAM_IO_CHUNK```: Data stream between IO and RF.
* ```STREAM_SRAM_CHUNK```: Data stream between SRAM and RF.
* ```STREAM_RF_CHUNK```: Data stream between RF and RF, between RF and DPU, and between DPU and DPU.
* ```STREAM_ADDR```: A stream of address made of integers.

#### Scalar Auxiliary Variable

Auxiliary variables are usually used for address calculation. They are not tight to any hardware resource. They can only be declared as ```int``` type at the moment.

!!! example
	Declare and use scalar auxiliary variables.
	```c++
	int i, j;
	for (i=0; i<10; i=i+1){
		j=i+1;
		...
	}
	```

### Primitive Function for Address Generation

Vesyla has certain reading or writing primitive functions to interact with storage variables. These primitive functions usually require address stream to specify which chunk of data is affected. To generate such address stream, you need to use primitive functions designed for address generation. Currently, three primitive functions are supported. The following example shows how to use these primitive functions to generate address stream.

!!! example
	Generate an address stream that contains only one address.
	```c++
	STREAM_ADDR addr = silago_agu_constant(1);
	```

	Generate a 1-d affine address stream that start from 0, increment by 1, and has 10 elements.
	```c++
	STREAM_ADDR addr = silago_agu_affine_1(0, 1, 10);
	```

	Generate a 2-d affine address stream that start from 0, increment by 1, and has 10 elements as level 1 and increment by 2 and has 20 elements as level 2.
	```c++
	STREAM_ADDR addr = silago_agu_affine_2(0, 1, 10, 2, 20);
	```

### Primitive Function for Arithmetic Operation

Certain type of arithmetic operations are supported by Vesyla. They are addition, subtraction, dot multiplication, etc. Other arithmetic operations need to be mapped to special DPU mode by primitive function call. We recommend to always use primitive function call to perform arithmetic operation. The following example shows how to use these primitive functions to perform arithmetic operation.

!!! example
	Perform operation: ``c = a + b``
	```c++
	#pragma bind dpu_0_0
	c = silago_dpu_add(a, b);
	```

Note that, you should always specify the binding information for arithmetic operations mapped to DPU. Otherwise, Vesyla will not be able to perform allocation and binding.

If a primitive function has multiple outputs, you can use the ``tie()`` function to bundle them to a tuple. For example:

!!! example
	Perform operation: ``e = a + b`` and ``f = c + d``
	```c++
	#pragma bind dpu_0_0
	tie(e, f) = silago_dpu_add_2(a, b, c, d);
	```

### Loop

Vesyla accept very restrict loop syntax to simplify the parsing process. A static loop should have constant start point, static increment as well as static iteration. If an expression that can be simplified to a constant number, it also considered as constant, hence can be used in static loop. Example below shows how to use a static loop.

!!! Example
	```c++ hl_lines="2 3"
	int n;
	n = 3;
	for (i=0; i<n+1; i=i+1){
		...
	end
	```

Vesyla support limited dynamic loops. Dynamic loop can have dynamic start point. But the iteration number should be constant.

Example of such dynamic loop is shown below:

!!! Example
	```c++ hl_lines="2"
	for (i=1; i<4; i=i+1)
		for (j=i; j<i+3; j=j+1)
			...
		end
	end
	```

!!! warning
	Vesyla does not support while-loop style dynamic loop with dynamic iteration number. Free style for-loop that implements a dynamic loop is not supported as well.

## Advanced Features

<!-- ### Branch

!!! warning
		Branch is not supported in current version. -->

<!-- ### Address Constraint

Address constraints are parameters used by address generation in AGU. Address constraints can be constant or RACCU variable calculated at run-time in RACCU. Dynamic address constraint variables are usually used in loops. Example below shows how to use a RACCU variable to serve as address constraint.

!!! Example
	```matlab hl_lines="3 5"
	x = [1:5]; %! REFI[0,0]
	y = [1:16]; %! REFI[0,0]
	a = 1; %! RACCU_VAR
	for i = 1:1:4
		y(a+1:a+1+5) = x(1:5) + y(a:a+5); %! DPU[0,0]
		a = a+1;
	end 
	```-->

<!-- ### Conflict-free Zone

When multiple operations need some common operands, due to the limit of the number of reading ports, those operations can't happen at the same time in normal condition. Resource sharing region tries to solve the problem. By enabling the broadcasting mechanism, all operation will recieve the same common operand at the same time generated by single reading port of the register file. The datapath of transmitting the common operand is now shared among those operations.

Resource sharing region requires a fixed datapath layout. Dynamic change of datapath structure is forbidden inside resource sharing region. So, you should only use it when needed.

Following example shows how to active resource sharing region.

!!! Example
	```matlab hl_lines="7 12"
	x0 = [1:5]; %! REFI[0,0]
	x1 = [1:5]; %! REFI[1,0]
	a2 = [1:5]; %! REFI[2,0]
	x3 = [1:5]; %! REFI[3,0]
	x4 = [1:5]; %! REFI[4,0]

	%! RESOURCE_SHARING_BEGIN
	x0 = x0 + a2; %! DPU[0,0]
	x1 = x1 + a2; %! DPU[1,0]
	x3 = x3 + a2; %! DPU[3,0]
	x4 = x4 + a2; %! DPU[4,0]
	%! RESOURCE_SHARING_END
	``` -->

### DPU Chain

Datapath can be configured as a chain of DPU operation. The output of the previous DPU will immediately enter the next DPU without any register file involved in between. Consider we want to compute a vector addition and a sigmoid function: $z = \sigma (x+y)$. We can employ two DPUs to perform the complete operation in pipelined fashion. By writing the matlab like the following, you can enable the feature.

!!! Example
	```c++
	#pragma bind dpu_0_0
	t = silago_dpu_mac(x, y);
	#pragma bind dpu_0_1
	z = silago_dpu_sigm(t);
	```

<!-- ### DPU Internal Scalar Register

Inside each DPU, there are **two** internal scalar registers which can be explicitly used via high-level matlab program. One can use them by declearing them with the pragma ```%! CDPU[row, col]```.

The available functions to load and store values to/from internal scalar registers are:

```matlab
r0 = silago_dpu_load_reg_0(x(1));
r1 = silago_dpu_load_reg_1(x(1));
[r0, r1] = silago_dpu_load_reg_both(x(1), x(2));
x(1) = silago_dpu_load_store_0(r0);
x(1) = silago_dpu_load_store_1(r1);
[x(1), y(1)] = silago_dpu_load_store_both(r0, r1);
```

!!! Warning
	Programmer should keep in mind that lifetime and physical location of those variable. Vesyla has very weak semantic checking on those internal scalar register variables.

!!! Example
	For example, if one want to calculate a function: $y = ax.y$. Instead of put the coefficient $a$ inside a normal register and waste other register entries of the same register block, you can put the coefficient to the internal register, and configure DPU to a scaled multiplication mode to get the correct result.

	```matlab hl_lines="6 9 12"
	a_mem = [1:16]; %! SRAM[0,0]
	x_mem = [1:16]; %! SRAM[0,0]
	y_mem = [1:16]; %! SRAM[0,0]
	x = [1:16]; %! REFI[0,0]
	y = [1:16]; %! REFI[0,0]
	r = zeros(1, 1); %! CDPU[0,0]

	x = a_mem;
	r = silago_dpu_load_reg_1(x(1));
	x = x_mem;
	y = y_mem;
	y = silago_dpu_scaled_mul(x, y, r); %! DPU[0,0]
	y_mem = y;
	``` -->

## A Complete Example

Here is a complete example of a Vesyla program. The program is a simple vector addition.

```c++
void model_l1(){
	// Read from input buffer
	STREAM_IO_CHUNK sab, sc;
	#pragma bind rf_0_0
	RF rf_0_0;
	sab=silago_io_read(__input_buffer__, silago_agu_affine_1(0,1,2));
	rf_0_0 = silago_rf_write_from_io_stream (sab, silago_agu_affine_1(0,1,2), rf_0_0);

	// Read from register file
	STREAM_RF_CHUNK aa, bb, cc;
	aa = silago_rf_read(rf_0_0, silago_agu_affine_1(0,1,16));
	bb = silago_rf_read(rf_0_0, silago_agu_affine_1(16,1,16));

	// Computation
	#pragma bind dpu_0_0
	cc = silago_dpu_add(aa, bb);

	// Write to register file
	rf_0_0 = silago_rf_write(cc, silago_agu_affine_1(0,1,16), rf_0_0);

	// Write to output buffer
	sc = silago_rf_read_to_io_stream (rf_0_0 , silago_agu_affine_1(0,1,1));
	__output_buffer__=silago_io_write(sc, silago_agu_affine_1(0,1,1), __output_buffer__);
}
```