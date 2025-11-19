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

PETSC is configured with Metis, Parmetis, and Scotch orderings, and to
support HYPRE, MUMPS, SuperLU, and SuperLU-DIST.
PETSc is compiled without OpenMP.

The `petsc` module defines the environment variable `PETSC_DIR` as the
root of the installation if this is required.


### Version history

=== "Upgrade 2025"

    - Module `petsc/3.24.1` added as non-default November 2025 (PE 23.09)
    - Module `petsc/3.18.5` installed as default May 2023 (PE 22.12)

    Note: `petsc/3.24.1` disables MUMPS support.

=== "Upgrade 2023"

    - Module `petsc/3.18.5` installed as default May 2023 (PE 22.12)
    - Module `petsc/3.14.2` recompiled May 2023 (PE 22.12)

    Note: PETSc has a number of dependencies; where applicable, the
    newer version of PETSc depends on the newer module version of each
    relevant dependency. Check `module list` to be sure.

=== "Full system"

    - Module `petsc/3.14.2` installed October 2021 (PE 21.04)

=== "4-cabinet system"

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
$ git checkout modules-2012-12
$ ./sh/tpsl/metis.sh --prefix=/path/to/install/location
$ ./sh/tpsl/parmetis.sh --prefix=/path/to/install/location
$ ./sh/tpsl/hypre.sh --prefix=/path/to/install/location
$ ./sh/tpsl/scotchv7.sh --prefix=/path/to/install/location
$ ./sh/tpsl/mumps.sh --prefix=/path/to/install/location
$ ./sh/tpsl/superlu.sh --prefix=/path/to/install/location
$ ./sh/tpsl/superlu-dist.sh --prefix=/path/to/install/location

$ module load cray-hdf5
$ ./sh/petsc.sh --prefix=/path/to/install/location
```
The `--prefix` option indicating the install directory should be the
same in all cases. See the Archer2 github repository for further
details (and options). This will compile version 3.18.5 against the
latest module versions of each dependency.


## Resources

[PETSc home page](https://petsc.org/release/)

Current [PETSc documentation](https://petsc.org/release/manual/)
(HTML)
