# HYPRE

HYPRE is a library of linear solvers for structured and unstructured
problems with a particular emphasis on multigrid. It is a product of
the Lawrence Livermore National Laboratory and is distrubted under
either the MIT license or the Apache license.


## Compiling and linking with HYPRE

- `module load hypre`

To compile and link an application with the HYPRE libraries, load the
`hypre` module and use the compiler wrappers `cc`, `CC`, or `ftn` in
the usual way. The relevant include files and libraries will be
introduced automatically.

Two versions of HYPRE are included: one with, and one without, OpenMP.
The relevant version will be selected if e.g., `-fopenmp` is included
in the compile or link stage.

The `hypre` module defines the environment variable `HYPRE_DIR` which
will show the root of the installation for the current programming
environment if required.


### Version history

=== "Full system"
    
    - Module `hypre/2.18.0` installed October 2021 (PE 21.04)
    
=== "4-cabinet system"
    
    - Module `hypre/2.18.0` installed January 2021

## Compiling your own version

The current supported version on Archer2 can be built using the script
from the Archer2 repository:
```
$ git clone https://github.com/ARCHER2-HPC/pe-scripts.git
$ cd pe-scripts
$ git checkout modules-2021-10
$ ./sh/tpsl/hypre.sh --prefix=/path/to/install/location
```
where the `--prefix` option determines the install directory. See the
Archer2 [github repository](https://github.com/ARCHER2-HPC/pe-scripts/tree/cse-develop) for more information.

## Resources

HYPRE [home page](https://computing.llnl.gov/projects/hypre-scalable-linear-solvers-multigrid-methods)

The latest HYPRE [user manual](https://hypre.readthedocs.io/en/latest/) (HTML)

An older [pdf version](https://computing.llnl.gov/sites/default/files/public/hypre-2.11.2_usr_manual.pdf)

HYPRE [github repository](https://github.com/hypre-space/hypre)
