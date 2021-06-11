# PETSc

PETSc is a suite of parallel tools for solution of partial differential
equations. PETSc is developed at Argonne National Laboratory and is
freely available under a BSD 2-clause license.

## Build

- `module load petsc`

Applications may be linked against PETSc by loading the `petsc` module
and using the compiler wrappers `cc`, `CC`, and `ftn` in the usual way.
Details of options introduced by the compiler wrappers can be
examined via, e.g.,
```
$ cc --cray-print-opts
```

PETSc is compiled with OpenMP.

The `petsc` module defines the environment variable `PETSC_DIR` as the
root of the installation if this is required.


### Version history

- Module `petsc/3.13.3` installed January 2021

    Known issues: PETSc is not currently available for `PrgEnv-aocc`.
    There is no HYPRE support in this version.


## Compile your own version

It is possible to follow the steps used to build the current version
on Archer2. These steps are codified at the Archer2
[github repository](https://github.com/ARCHER2-HPC/pe-scripts/tree/cse-develop)
and include a number of dependencies to be built in the correct order:
```
$ git clone https://github.com/ARCHER2-HPC/pe-scripts.git
$ cd pe-scripts
$ git checkout cse-develop
$ ./sh/tpsl/metis.sh --prefix=/path/to/install/location
$ ./sh/tpsl/parmetis.sh --prefix=/path/to/install/location
$ ./sh/tpsl/scotch.sh --prefix=/path/to/install/location
$ ./sh/tpsl/mumps.sh --prefix=/path/to/install/location
$ ./sh/tpsl/superlu.sh --prefix=/path/to/install/location
$ ./sh/tpsl/superlu-dist.sh --prefix=/path/to/install/location

$ module load cray-hdf5
$ ./sh/petsc.sh --prefix=/path/to/install/location
```
The `--prefix` option indicating the install directory should be the
same in all cases. See the Archer2 github repository for further
details (and options).


## Resources

PETSc [home page](https://www.mcs.anl.gov/petsc/)

PETSc [documentation](https://www.mcs.anl.gov/petsc/documentation/index.html)
(HTML)

Current
[user manual](https://www.mcs.anl.gov/petsc/petsc-current/docs/manual.pdf)
(pdf)
