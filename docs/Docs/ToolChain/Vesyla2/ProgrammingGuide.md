# Programming Guide

## Top-level Function Declaration

The top-level function is always defined as following:
``` c++
void model_l1() {
...
}
```

The function name ```model_l1``` means this is the level-1 of computation model. The level-0 model is a free-style C++ model that defines the desired computation behavior. The level-1 model is a untimed C model that directly reflects the hardware mapping. The level-2 model is cycle-accurate model defined in either HDL or systemC.

The compiler creates cycle-accurate configuration that can be co-simulated with level-2 model. The input of the compiler is level-1 model.

## Global Variables

The global variables model the SRAM. Once the architecture is defined, these global variables is also defined. The global variables are:

```
SRAM used as I/O:
  __sram_vector_0__
  __sram_vector_1__
  ...
```

## Macros

You can use macros. They macros are support through inja (a C++ binding for Jinja). Read more about macros in the [Macro Expansion Process](../Macro/) section.

## Pre-defined Primitive Functions

There are many pre-defined primitive functions that directly reflect the hardware implementation. They are defined in [Primitive Function](../PrimitiveFunction/) section. You should use those primitive functions to move data, rearrange words and perform computation.

## Temporary Variables

All temporary variables, except for address variables, need to be bind to some actual storage device. The storage location can either be VWR or internal registers in VFU. The binding information is indicated by binding pragma.

!!! Example
    Binding a variable named ``A`` to ``VWR_0``.
	``` c++
	#pragma bind vwr_0
	VwrVector A;
	```

	Binding a variable named ``r`` to internal register ``R_0``(``R_IN``) of ``VFU1``.
	```c++
	#pragma bind r_1_0
	VwrVector r;
	```


## Example

Here we show a program that implement a simple vector addition. Vector ``A``, ``B`` and ``C`` are both 16-word vector. Each word is 16-bit. They vector addition is described by equation: ```C = A + B```.

### SRAM Organization

```
A[00], A[01], ..., A[07],
A[08], A[09], ..., A[15],
B[00], B[01], ..., B[07],
B[08], B[09], ..., B[15],
C[00], C[01], ..., C[07],
C[08], C[09], ..., C[15],
```

### Architecture Definiation

```
#define ARCH_SRAM_NUM 1
#define ARCH_VWR_NUM 2
#define ARCH_VFU_NUM 2

#define ARCH_SRAM_CHUNK_SIZE 128
#define ARCH_VWR_CHUNK_SIZE 32
#define ARCH_REG_CHUNK_SIZE 16

#define ARCH_SRAM_VECTOR_SIZE 6
#define ARCH_VWR_VECTOR_SIZE 4
#define ARCH_REG_VECTOR_SIZE 2
```

This defination defines the following architecture:

SRAM:
```
[[128b], [128b], [128b], [128b], [128b], [128b]]
```

VWR:
```
[[32b], [32b], [32b], [32b]]
```

Registers inside DPU:
```
[[16b], [16b]]
```

### Algorithm

``` c++
void model_l1() {
	#pragma bind vwr_0
	VwrVector A;
	#pragma bind vwr_1
	VwrVector B;
	#pragma bind
	
	{% for x in range(ARCH_VFU_NUM) %}
	#pragma bind r_{{x}}_0
	RegVector r_{{x}}_in;
	#pragma bind r_{{x}}_3
	RegVector r_{{x}}_out;
	{% endfor %}

	for(int i=0; i<2; i=i+1){
		// Read A and B vector from SRAM to VWR
		RLB(__sram_vector_0__, A, i, 0);
		RLb(__sram_vector_0__, B, i+2, 0);

		for(int j=0; j<{{ARCH_VWR_SIZE/ARCH_VFU_NUM}}; j=j+1){
			#pragma begin conflic_free_zone
			{% for x in range(ARCH_VFU_NUM) %}
			// Read chunk of A in R_in
			VMV({{x}}, A, r_{{x}}_in, j);
			// Addition with B
			VFUX_RV_R({{x}}, 0, 0, R_{{x}}_in, B, j, r_{{x}}_out);
			// Write the result to VWR. Reuse A to store the result.
			RMV({{x}}, A, r_{{x}}_out, j);
			{% endfor %}
			#pragma end conflict_free_zone
		}
		
		// Write result to SRAM
		WLB(__sram_vector_0__, A, i+4, 0);
	}
}

```
