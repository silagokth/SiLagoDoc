# Vesyla Programming Guide (v2)

!!! note
    This page is written for vesyla-suite **version 2**. It's deprecated and will be removed soon. You should only use it for old project such as DRRA characterization. For vesyla-suite version 3, please see [Vesyla Programming Guide](../../v3/VesylaProgrammingGuide).

## Basics

### General Guide

Vesyla accept modified matlab code as input language. You shouldn't write the matlab code like a programming language. You should instead use it as a tool to model the behaviour of the hardware. Vesyla supports small portion of matlab grammar. There are some generic rules expressing the programming style vesyla accepts.

* General function call is not allowed unless the function is predefined as primitive function.
* Variable except for constant variable and loop iterator should always be decleared via pragma.
* Statement should always ends with semicolon (```;```) to avoid unexpected outputs while simulating in matlab.
* Constant number is normally treated as integers, not double floating point.

### Pragma

Pragma is the notation that guides Vesyla during synthesis process. Vesyla recongnize pragma starting with symbols ```%!```. The main function of pragmas is specify allocation and binding information since Vesyla can't perform automatic allocation and binding. Section [Variable Declaration](#variable-declaration), [Arithmetic Operation](#arithmetic-operation), [Address Constraint](#address-constraint) [DPU Chain](#dpu_chain) and [DPU Internal Scalar Register](#dpu-internal-scalar-register) describe how to use pragmas to allocate and bind resources. Some other usage of pragma also exist, check section [Resource Sharing Region](#resource-sharing-region) for more detail.

### Variable Declaration

Variables supported by Vesyla are vectored Register file variables and SRAM variables. Register file variables will bind to register file and SRAM variables will bind to DiMArch. Since matlab dosen't require variable declaration, we need to give initial value to declare them. You can use the standard initialization assignment for matlab **1-D** arry to declare a variable. Function such as ```zeros()``` and  ```ones()``` are also supported.

To declear SRAM variable, you need to use ```%! MEM[row, col]``` pragma. ```row``` and ```col``` are the coordinate of the SRAM block you want to bind for this variable. SRAM usually organized as a 2-D bit matrix without bit-level and word-level access. Data communication with SRAM happens in bulk mode where each data exchange need to be a complete SRAM row. Suppose SRAM with is $N$ and Register width (1 word) is $M$ bit. Each SRAM row will have $N/M$ words. Typical value for $M$ and $N$ are $M=256$ and $N=16$. Since SRAM only support whole line reading and writing, the SRAM variable you defined should always have multiple of $M/N$ elements.

!!! Example
	The example shows how to define a SRAM variable ```y``` on SRAM block ```[0,0]```. ```y``` is initialized by a 1-D vector ```[1,2,3,4,5,...,16]```.
	```matlab
	y = [1:16]; %! MEM[0,0]
	```

To declear register file variable, you need to use ```%! RFIL[row, col]``` pragma. ```row``` and ```col``` are the coordinate of the DRRA cell you want to bind for this variable. Unlike SRAM, register file is organized in the way that each row will always represent a word. So there is no restriction on the size of register file variable as long as it doesn't exceed the register depth limit.

!!! Example
	The example shows how to define a register file variable ```x``` on DRRA cell ```[0,0]```.```x``` has 5 elements and is initialized by a ```zeros()``` function which will set all elements in ```x``` to ```0```.
	```matlab
	x = zeros(1, 5); %! REFI[0,0]
	```
For debugging purpose, vesyla allows initialization of register file variable. However, register should not be initialized with any value other than zeros because the real DRRA cell doesn't have interface to support the register initalization. Register file variables hence should always get data from SRAM variables.

!!! Error
	The following variable declaration style is incorrect:
	
	1. No storage pragma specified
	```matlab
	x = zeros(1, 5);
	```
	2. Initialized to scalar
	```matlab
	x = 1; %! RFILE[1,1]
	```
	3. Initialized to 2-D array
	```matlab
	x = ones(2,3); %! RFILE[1,1]
	```
	4. SRAM varible is not the size of SRAM row
	```matlab
	x = [1:3]; %! MEM[0,1]
	```
	5. Initialization function ```randi()``` is not supported
	```matlab
	x = randi(10, 1, 16); %! MEM[0,1]
	```

### Vector Slicing and Address Generation

Each DPU can only process one scalar data each time, so the vectored register variable should be slice first before sending to DPU. The slicing operation is mapped on AGU by REFI instruction. While writing matlab code, you don't have to worry about the slicing since matlab directly support vector slicing.

Here is an example of slicing a vector:

!!! Example
	```matlab hl_lines="3"
	x = [1:5]; %! REFI[0,0]
	y = [1:5]; %! REFI[0,0]
	y(1:5) = x(1:5) + y(1:5); %! DPU[0,0]
	```

If you don't use any slicing and directly feed a vectored register variable to arithmetic operaion, Vesyla will use the full range of that variable.

When slicing a SRAM varialbe, the minimal slice should always be multiple of 16.

Except for the matlab default slicing method, you can also use two primitive AGU function to linear slice a vectored varialbe both in 1-D or 2-D. Example is given below. All address sequence in the following example are "1,2,3,4,5".

!!! Example
	```matlab hl_lines="3"
	x = [1:5]; %! REFI[0,0]
	y = [1:5]; %! REFI[0,0]
	y(1:5) = x(silago_agu_linear_1d(1,1,5)) + y(silago_agu_linear_2d(1,0,1,1,5)); %! DPU[0,0]
	```


### Arithmetic Operation

Certain type of arithmetic operations are supported by Vesyla. They are addition, subtraction, dot multiplication, sum, abs, etc. Special arithmetic operation need to be mapped to special DPU mode by primitive function call, see section [Primitive Function](#primitive-function).

!!! Bug
	Symbol ```~``` is not supported yet!

For arithmetic assignment, you can have a multiple variables as output depending on the DPU mode. The ignored output can be muted by symbol ```~```.

Arithmetic operation need a computation resource to perform required operation, that is the DPU. So, every arithmetic operation need to bind to a DPU resource via pragma.

Example of an arithmetic assignment is demonstrated as following:

!!! Example
	```matlab hl_lines="3"
	x = [1:5]; %! REFI[0,0]
	y = [1:5]; %! REFI[0,0]
	y(1:5) = x(1:5) + y(1:5); %! DPU[0,0]
	```

### Static Loop

Vesyla accept all static loops. A static loop should have constant start point, static increment as well as static iteration. If an expression that can be simplified to a constant number, it also considered as constant, hence can be used in static loop. Example below shows how to use a static loop.

!!! Example
	```matlab hl_lines="2 4"
	n = 3;
	for i = 1:1:n+1
		...
	end
	```

### Dynamic Loop

Vesyla support limited dynamic loops. Dynamic loop can have dynamic start point, and dynamic iteration. However, those number should be in address domain, a.k.a computed by RACCU and is fully determinastic after unrolling all the loops.

Example of such dynamic loop is shown below:

!!! Example
	```matlab hl_lines="2"
	for i = 1:1:4
		for j = i:1:i+3
			...
		end
	end
	```

### Branch

Vesyla support normal matlab branch except for both operands of condition are constants. The usage of branch is the same as the original matlab code. For example:

!!! Example
	```matlab hl_lines="4 6 8"
	x = [1:5]; %! REFI[0,0]
	y = [1:5]; %! REFI[0,0]
	w = [3, 5]; %! REFI[0,0]
	if w(1) > w(2)
		y = x;
	else
		y = x+y; %! DPU[0,0]
	end
	```

### Address Constraint

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
	```

## Advanced Features

### Macro

Vesyla support symbolic expression to enable fast design space exploration. One of the technique is to use macros. Before the lexecal analysis, vesyla will expand all macro to normal program code. Macro gives programmer the tool to generate multiple program with small variations.

Programmer need to provide a template and a series of data. Data is organized in json format and will be loaded in to evaluate those macros defined in template. Template use a grammar like the templating package [inja](https://github.com/pantor/inja). Infact, vesyla directly use inja library to evaluate macros.

Example below demonstrate how to define a loop in template:

!!! Example
	An template file defined as following:
	
	```matlab
	{% for x in range(par_col) %}
	x0_mem_{{x}} = [1 : n/col]; %! MEM[0, {{x}}]
	y0_mem_{{x}} = [1 : n/col]; %! MEM[0, {{x}}]
	{% endfor %}
	```
	
	With a json-formated data file:
	
	```json
	{"par_col" : 2}
	```
	
	This template will generate a real matlab code as following by expanding the FOR-LOOP macro:

	```matlab
	x0_mem_0 = [1 : n/col]; %! MEM[0, 0]
	y0_mem_0 = [1 : n/col]; %! MEM[0, 0]
	x0_mem_1 = [1 : n/col]; %! MEM[0, 1]
	y0_mem_1 = [1 : n/col]; %! MEM[0, 1]
	```

!!! Tip
	More complex usage please visit [inja](https://github.com/pantor/inja) website.

### Primitive Function

Primitive DPU functions are functions that corresponds to a complete DPU mode. Different DPUs targeting on different application domain may have some special modes specifically made for such application domain. For example, sigmoid function for neural network application. Those primitive function is not directly supported by matlab, but they are supported by vesyla.

To use a specific DPU mode as primitive function, first you need to make sure the DRRA cell you are using has such mode. Then you need to change the configuration of vesyla to recongnize such mode. The configuration file is *$Vesyla_root/config/primitive_func_def.xml*. Finally, you can use the function inside your program.

All primitive DPU functions have name should start with *silago_dpu_* to be accepted by Vesyla.

Example of using primitive DPU function:

!!! Example
	```matlab hl_lines="2"
	x = [1:5]; %! REFI[0,0]
	x = silago_dpu_sigmoid(x); %! DPU[0,0]
	```

AUGs also have special primitive functions to express the complex addressing mode. But AGU primitive functions are not custom. There are two AGU primitive function: **silago_agu_linear_1d()** and **silago_agu_linear_2d()**. More AGU primitive functions will be added if some application domain requires.

Example of using primitive DPU function:

!!! Example
	```matlab hl_lines="3"
	x = [1:5]; %! REFI[0,0]
	a = [1]; %! REFI[0,0]
	x = x + a(silago_agu_linear_1d(1, 0, 5)); %! DPU[0,0]
	```

### Resource Sharing Region

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
	```

### DPU Chain

Datapath can be configured as a chain of DPU operation. The output of the previous DPU will immediately enter the next DPU without any register file involved in between. Consider we want to compute a vector addition and a sigmoid function: $z = \sigma (x+y)$. We can employ two DPUs to perform the complete operation in pipelined fashion. By writing the matlab like the following, you can enable the feature.

!!! Example
	```matlab hl_lines="4 6 7"
	x = [1:5]; %! REFI[0,0]
	y = [1:5]; %! REFI[0,0]
	z = [1:5]; %! REFI[0,0]
	t = zeros(1, 5); %! CDPU[0,0]

	t = x + y;
	z = silago_dpu_sigmoid(t); %! DPU[1,0]
	```

### DPU Internal Scalar Register

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
	```

## Not Supported

Some matlab code is not accepted by Vesyla because it can't execute on DRRA fabric. They are:

- While-loop.
- For-loop inside branch.
- Arithmetic statement that can't be mapped to single DPU mode.
- Normal function call except for primitive function call.
- Indirect addressing.
