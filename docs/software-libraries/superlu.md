# SuperLU and SuperLU_DIST

SuperLU and SuperLU_DIST are libraies for the direct solution of large
sparse non-symmetric systems of linear equations, typically by factorisation
and back-substitution. The libraries are provided by Lawrence Berkeley
National Laboratory and are freely available under a slightly modified
BSD-style license.

Two separate modules are provided for SuperLU and SuperLU_DIST.

## SuperLU

- `module load superlu`

This module provides the serial library SuperLU.

### Compiling and linking with SuperLU

Compiling and linking SuperLU applications requires no special action
beyond `module load superlu` and using the standard compiler wrappers
`cc`, `CC`, or `ftn`. The exact options issued by the compiler
wrapper can be examined via, e.g.,
```
$ cc --cray-print-opts
```
while the module is loaded.

The module defines the environment variable `SUPERLU_DIR` as the root
location of the installation for a given programming environment.

### Version history

=== "Full system"
    
    - Module `superlu/5.2.2` installed October 2021 (PE 21.04)
    
=== "4-cabinet system"
    
    - Module `superle/5.2.1` installed January 2021

## SuperLU_DIST

- `module load superlu-dist`

This modules provides the distrubuted memory parallel library SuperLU_DIST
both with and without OpenMP.

### Compiling and linking SuperLU_DIST

Use the standard compiler wrappers:
```
$ cc my_superlu_dist_application.c
```
or
```
$ cc -fopenmp my_superlu_dist_application.c
```
to compile the and link against the appropriate libraries.

The `superlu-dist` module defines the environment variable `SUPERLU_DIST_DIR`
as the root of the installation for the current programming environment.


### Version history

=== "Full system"
    
    - Module `superlu-dist/6.4.0` installed October 2021 (PE 21.04)
    
=== "4-cabinet system"
    
    - Module `superlu-dist/6.1.1` installed January 2021


## Compiling your own version

The build used for Archer2 can be replicated by using the scripts
provided at the Archer2 repository.

### SuperLU

The current Archer2 supported version may be built via
```
$ git clone https://github.com/ARCHER2-HPC/pe-scripts.git
$ cd pe-scripts
$ git checkout modules-2021-10
$ ./sh/tpsl/superlu.sh --prefix=/path/to/install/location
```
where the `--prefix` option controls the install destination.

### SuperLU_DIST

SuperLU_DIST is configured using Metis and Parmetis, so these
should be installed first:
```
$ ./sh/tpsl/metis.sh --prefix=/path/to/install/location
$ ./sh/tpsl/parmetis.sh --prefix=/path/to/install/location
$ ./sh/tpsl/superlu_dist.sh --prefix=/path/to/install/location
```
will download, compile, and install the relevant libraries.
The install location should be the same for all three packages.
See the Archer2
[github repository](https://github.com/ARCHER2-HPC/pe-scripts/tree/cse-develop)
for further options and details.

## Resources

The Supernodal LU [project home page](https://portal.nersc.gov/project/sparse/superlu/)


The SuperLU [User guide](https://portal.nersc.gov/project/sparse/superlu/ug.pdf) (pdf). This describes both SuperLU and SuperLU_DIST.


The SuperLU [github repository](https://github.com/xiaoyeli/superlu)

The SuperLU_DIST [github repository](https://github.com/xiaoyeli/superlu_dist)
