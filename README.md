Source code repository: https://gitlab.xlim.fr/hominc/HomInc

HOMINC
======

C++ Library for homology computation based on effective homology notions. In particular, it provides an implementation of :
+ the notion of homological equivalence
+ the modification of a chain complex given an identification operation
+ the short exact sequence theorem in the case of an identification

This work, and its terminology, are based on [[1]](#ref1) et [[2]](#ref2).

Dependencies
-----------
  - cmake
	- CHomP (provided)
	- Boost (SERIALIZATION, THREAD)
	- GMP (MPIR on Windows)
	- GoogleTest (provided)

A modified version of CHomP is used and is available in the source code (used as header only). The original CHomP project is available [here](https://github.com/shaunharker/CHomP).

Installation
------------

### Windows

#### Boost
The sub-libraries `SERIALIZATION` and `THREAD` of Boost shall be installed using, in your `BOOST_ROOT` directory:

	b2.exe --with-serialization --with-thread --build-type=complete

We recommend adding `<BOOST_ROOT>/stage/lib` to your `PATH` variable so that the `.dll` are found when calling `HomInc_Benchmark.exe` and `HomIncTester.exe`

Note: `b2.exe` is available after using `bootstrap.bat` (cf. [Boost documentation](https://www.boost.org/doc/libs/1_79_0/more/getting_started/windows.html#prepare-to-use-a-boost-library-binary))

#### MPIR

The easiest way to install MPIR is by using [vcpkg](https://github.com/microsoft/vcpkg):

    vcpkg.exe install mpir

Check that the static library `<VCPKG_ROOT>/installed/x64-windows/mpir.lib` exists.

#### Generate a solution
To build the project, we defined a CMAKE configuration file in `./src/CMakeLists.txt`. Simply use:

	cmake -S ./src -B ./build -DMPIR_LIB=<VCPKG_ROOT>/installed/x64-windows/mpir.lib

The solution should be in the newly created folder `./build`.

Note: at the time of writing this documentation, the package `mpir` of vcpkg does not provide any cmake configuration. We are using `MPIR_LIB=<VCPKG_ROOT>/installed/x64-windows/mpir.lib` for this reason.

#### Check the installation

Compile and use `HomIncTester.exe` in order to ensure everything is working correctly.

### Linux
TODO
### MacOS
TODO

Usage
-----------
The project is divided in 3:
- `HomInc` is the library
- `HomInc_Benchmark` is a program to benchmark `HomInc`
- `HomIncTester` is designed to functionally test `HomInc`

### HomInc
`HomInc` is the library implementing the notions of the short exact sequence theorem. It is used as a static library to compile `HomIncTester` and `HomInc_Benchmark`.

### HomIncTester
`HomIncTester` is a functional testing software based on GoogleTest.

### HomInc_Benchmark
`HomInc_Benchmark` is designed as an example of a program using `HomInc`. Its goal is to benchmark `HomInc`.

The benchmark consists in using a succession of operations on a chain complex. We call this succession a *construction process*.

At each step, `HomInc_Benchmark` displays the betti numbers and the torsion coefficients of the chain complex in use.

Use `-o` to maintain a whole homological equivalence [^a].

Two modes are available:
1. (d,e)-grids sewing along a (n,e)-grid, with d > n. For example:

  `HomInc_Benchmark -g 3 2 1`

2. User-defined operations and chain complex. For example:

  `HomInc_Benchmark <HOMINC>/data/simplexe.hominc <HOMINC>/data/simplexe.cprocess`


[^a]: All the morphisms involved in a homological equivalence are then computed. This allows to express a chain from any of its chain complexes in the basis of the others. For example, this can be used to find a generator of the homology groups.

Inputs
---------------------------
Some inputs are given as examples in the folder  `<HOMINC>/data`. There is two kind of user defined inputs: chain complexes and construction processes.

### Chain Complexes

The chain complexes are defined by giving the p-boundaries in the ascending order. Defining a dimension always start with a row:

	#<p> <number of p-generators>

The p-boundaries are then given line by line. A p-boundary line is of the form:

	<p-generator name> [<coefficient> <name of a (p-1)-generator> ...]

For example, a chain complex induced by a triangle is given by:

	#0 3
	#1 3
	A 1 0 -1 1
	B 1 2 -1 1
	C 1 0 -1 2
	#2 1
	alpha 1 A -1 B -1 C

Edges are named `A`, `B` and `C` and `alpha` is the face. The boundary of `alpha` is the chain `A-B-C`.

### Construction processes

A construction process is defined operation by operation.

The first line of an operation declaration is of the form:

    #<Operation type>

There are two valid values for the operation type:
  - `I`, to define an identification
  - `D`, to define the inverse of an identification

The operation is then defined as an identification, by ascending dimension. Such a line is of the form:

	<p> {<List of p-cells identified with each others>}{<List of p-cells identified with each others>}  ...

For example, to identify vertices `1` and `2` of the chain complex induced by a triangle, given in the previous section, we use:

	#I
	0 {1,2}

Note: for now, `HomInc` is working only with construction processes composed only of identifications.

Scripts
-------
We provide some scripts to visualize the data generated by the benchmark, such as time computation per construction step.

Note: these scripts are provided "as is". They may be outdated.

Reference
-------------
<a name="ref1"></a>[1]: Rubio, J., & Sergeraert, F. (2012). Constructive homological algebra and applications. arXiv preprint arXiv:1208.3816.

<a name="ref2"></a>[2]: Alayrangues, S., Fuchs, L., Lienhardt, P., & Peltier, S. (2015). Incremental Computation of the Homology of Generalized Maps: An Application of Effective Homology Results.
