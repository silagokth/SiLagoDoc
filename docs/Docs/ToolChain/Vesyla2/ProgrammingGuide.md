# Programming Guide

## Top-level Function Declaration

The top-level function is always defined as following:
``` c++
void model_l2() {
...
}
```

The function name ```model_l2``` means this is the 2nd level of computation model. The level 1 model is a free-style C++ model that defines the desired computation behavior. The level 2 model is a untimed C model that directly reflects the hardware mapping. The level 3 model is cycle-accurate model defined in either HDL or systemC.

The compiler creates cycle-accurate configuration that can be co-simulated with level 3 model. The input of the compiler is level 2 model.

## Global Variables

The global variables model the SRAM, the VWRs and the internal registers inside each DPU. Once the architecture is defined, these global variables is also defined. The global variables are:

```
SRAM used as I/O:
  __sram_vector_0__

VWRs:
  __vwr_vector_0__
  __vwr_vector_1__
  __vwr_vector_2__
  ...

REGISTERS inside DPU:
  __reg_vector_r1_0__
  __reg_vector_r2_0__
  __reg_vector_r3_0__
  __reg_vector_r4_0__
  __reg_vector_r1_1__
  __reg_vector_r2_2__
  __reg_vector_r3_3__
  __reg_vector_r4_4__
  ...
```

!!! Warning
	The idea of defining VWRs and Registers as global variables might not be a good idea. It could be replaced by binding pragma associated with temporary variable declaration.

## Pre-defined Primitive Functions

### Data Transfer

``` c++
// SRAM -> VWR
void read_sram_to_vwr(SramVector sram_name, int sram_addr, VwrVector& vwr_name, int shuffle_mode);
// VWR -> SRAM
void write_sram_from_vwr(SramVector& sram_name, int sram_addr, VwrVector vwr_name, int shuffle_mode);
// Read VWR content in parellel
void read_vwr(VwrVector vwr_name, RegVector& r0, RegVector& r1, ...);
// Write VWR content in parellel
void write_vwr(VwrVector& vwr_name, RegVector r0, RegVector r1, ...);
```

### Computation

``` c++
// DPU operation r0+r1->r2
void dpu_add(RegVector r0, RegVector r1, RegVector& r2);
// DPU operation r0*r1->r2
void dpu_mul(RegVector r0, RegVector r1, RegVector& r2);
// Shuffle in DPU: shuffle(r0) -> r1
void shuffle(RegVector r0, RegVector& r1);
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
#define ARCH_DPU_NUM 8

#define ARCH_SRAM_CHUNK_SIZE 128
#define ARCH_VWR_CHUNK_SIZE 16
#define ARCH_REG_CHUNK_SIZE 16

#define ARCH_SRAM_VECTOR_SIZE 6
#define ARCH_VWR_VECTOR_SIZE 8
#define ARCH_REG_VECTOR_SIZE 1
```

This defination defines the following architecture:

SRAM:
```
[[128b], [128b], [128b], [128b], [128b], [128b]]
```

VWR:
```
[[16b], [16b], [16b], [16b], [16b], [16b], [16b], [16b]]
```

Registers inside DPU:
```
[[16b]]
```

### Algorithm

``` c++
void model_l2() {
	for(int i=0; i<2; i++){
		// Read A and B vector from SRAM to VWR
		read_sram_to_vwr(__sram_vector_0__, i, __vwr_vector_0__);
		read_sram_to_vwr(__sram_vector_0__, i+2, __vwr_vector_1__);

		// Create temp variables
		RegVector _x0, _x1, _x2, _x3, _x4, _x5, _x6, _x7;
		RegVector _y0, _y1, _y2, _y3, _y4, _y5, _y6, _y7;

		// Read A in VWR0 to R1 registers
		read_vwr(__vwr_vector_0__, _x0, _x1, _x2, _x3, _x4, 
			_x5, _x6, _x7);
		
		write_r1(__reg_vector_r1_0__, _x0);
		write_r1(__reg_vector_r1_1__, _x1);
		write_r1(__reg_vector_r1_2__, _x2);
		write_r1(__reg_vector_r1_3__, _x3);
		write_r1(__reg_vector_r1_4__, _x4);
		write_r1(__reg_vector_r1_5__, _x5);
		write_r1(__reg_vector_r1_6__, _x6);
		write_r1(__reg_vector_r1_7__, _x7);
		
		// Read B in VWR1 to temp variables
		read_vwr(__vwr_vector_1__, _y0, _y1, _y2, _y3, _y4, 
			_y5, _y6, _y7); 
		
		// Preform Addition
		read_r1(__reg_vector_r1_0__, _x0);
		read_r1(__reg_vector_r1_1__, _x1);
		read_r1(__reg_vector_r1_2__, _x2);
		read_r1(__reg_vector_r1_3__, _x3);
		read_r1(__reg_vector_r1_4__, _x4);
		read_r1(__reg_vector_r1_5__, _x5);
		read_r1(__reg_vector_r1_6__, _x6);
		read_r1(__reg_vector_r1_7__, _x7);

		dpu_add(_x0, _y0, _y0);
		dpu_add(_x1, _y1, _y1);
		dpu_add(_x2, _y2, _y2);
		dpu_add(_x3, _y3, _y3);
		dpu_add(_x4, _y4, _y4);
		dpu_add(_x5, _y5, _y5);
		dpu_add(_x6, _y6, _y6);
		dpu_add(_x7, _y7, _y7);

		// Write back the result from temp variables to VWR0, this is C vector
		write_vwr(__vwr_vector_0__, _y0, _y1, _y2, _y3, _y4,
			_y5, _y6, _y7);
		
		// Write back C vector to SRAM
		write_sram_from_vwr(___sram_vector_0__, i+4, __vwr_vector_0__);
	}
}

```
