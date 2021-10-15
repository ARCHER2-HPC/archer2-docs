# MUMPS

MUMPS is a parallel solver for large sparse systems and features
a 'multifrontal' method and is developed largely at CERFCAS,
ENS Lyon, IRIT Toulouse, INRIA, and the University of Bordeaux.
It is provided free of charge and is largely under a CeCILL-C
lisence.


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
$ git checkout modules-2021-10
$ ./sh/tpsl/metis.sh --prefix=/path/to/install/location
$ ./sh/tpsl/parmetis.sh --prefix=/path/to/install/location
$ ./sh/tpsl/scotch.sh --prefix=/path/to/install/location
$ ./sh/tpsl/mumps.sh --prefix=/path/to/install/location
```
where the `--prefix` option should be the same for MUMPS at
the three dependecies (Metis, Parmetis, and Scotch). See the
Archer2 [github repository](https://github.com/ARCHER2-HPC/pe-scripts/tree/cse-develop)
for further options and details.


## Resources

The MUMPS [home page](http://mumps.enseeiht.fr)

MUMPS [user manual](http://mumps.enseeiht.fr/doc/userguide_5.4.1.pdf) (pdf)
