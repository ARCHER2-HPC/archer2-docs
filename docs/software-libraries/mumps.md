# MUMPS

MUMPS is a parallel solver for large sparse systems and features
a 'multifrontal' method and is developed largely at CERFCAS,
ENS Lyon, IRIT Toulouse, INRIA, and the University of Bordeaux.
It is provided free of charge and is largely under a CeCILL-C
license.


## Compiling and linking with MUMPS

- `module load mumps`

To compile an application against the MUMPS libraries, load the `mumps`
module and use the compiler wrappers `cc`, `CC`, and `ftn` in the
usual way.

MUMPS is configured to allow Pord, Metis, Parmetis, and Scotch orderings.

Two versions of MUMPS are provided: one with, and one without, OpenMP.
The relevant version will be selected if the relevant option is
included at the compile stage.

The `mumps` module defines `MUMPS_DIR` which locates the root of the
installation for the current programming environment.


### Version history

=== "Upgrade 2025"

    - Module `mumps/5.8.1` added as non-default November 2025 (PE 23.09)
    - Module `mumps/5.5.1` installed as default May 2023 (PE 22.12)

=== "Upgrade 2023"

    - Module `mumps/5.5.1` installed as default May 2023 (PE 22.12)
    - Module `mumps/5.3.5` recompiled May 2023 (PE 22.12)

    Note: `mumps/5.5.1` uses `scotch/7.0.3` while `mumps/5.3.5` uses
    `scotch/6.1.0`.

=== "Full system"

    - Module `mumps/5.3.5` installed October 2021 (PE 21.04)

=== "4-cabinet system"

    - Module `mumps/5.2.1` installed January 2021

    Known issues:
    The OpenMP version in `PrgEnv-aocc` is not available at the moment.


## Compiling your own version

The current supported version of MUMPS on Archer2 can be compiled using
a script available from the Archer githug repository.
```
$ git clone https://github.com/ARCHER2-HPC/pe-scripts.git
$ cd pe-scripts
$ git checkout modules-2022-12
$ ./sh/tpsl/metis.sh --prefix=/path/to/install/location
$ ./sh/tpsl/parmetis.sh --prefix=/path/to/install/location
$ ./sh/tpsl/scotchv7.sh --prefix=/path/to/install/location
$ ./sh/tpsl/mumps.sh --prefix=/path/to/install/location
```
where the `--prefix` option should be the same for MUMPS at
the three dependencies (Metis, Parmetis, and Scotch Version 7).
See the Archer2 [github repository](https://github.com/ARCHER2-HPC/pe-scripts/tree/cse-develop)
for further options and details.


## Resources

The [MUMPS home page](https://mumps-solver.org/index.php)

MUMPS [user manual](https://mumps-solver.org/doc/userguide_5.8.1.pdf)
(Version 5.8.1, pdf)
