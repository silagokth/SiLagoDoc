# GEMM

## Function

```gemm``` computes a matrix-matrix product with general matrices.

The ```gemm``` routines compute a scalar-matrix-matrix product and add the result to a scalar-matrix product, with general matrices. The operation is defined as

$$
C \leftarrow \alpha *A*B + \beta *C
$$

where:

- $\alpha$ and $\beta$ are scalars,
- $A$, $B$ and $C$ are matrices:
	- $A$ is an m-by-k matrix,
	- $B$ is a k-by-n matrix,
	- $C$ is an m-by-n matrix.

## Parameters

$m$: The number of rows in $A$ and $C$.

$k$: The number of columns in $A$ and the number of rows in $B$.

$n$: The number of columns in $C$.

## Mapping

### One-Column Mapping

#### Memory Mapping

$m$, $k$ and $n$ are multiple of DiMArch row width (16). $A$ and $C$ stored in DiMArch in row-major fashion and $B$ stored in DiMArch in **col-major** fashion.



The first 16 words in register file in DRRA cell ```[0,0]``` is reserved for elements in $A$. The second half register file (16 words) is reserved for elements in $B$. The first 16 words in register file in DRRA cell ```[1,0]``` is for elements in $C$.

The register space for $A$ is also used temporarily for storing $\alpha$ and $\beta$.

One of the internal register in DRRA cell ```[0,0]``` is also used for holding $\alpha$ in order to perform the ```axpy()``` function.

#### Function Mapping



## Scaling

## Cost Metrics
