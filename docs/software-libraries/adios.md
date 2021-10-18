# ADIOS

The Adaptable I/O System (ADIOS) is developed at Oak Ridge National
Laboratory and is freely available under a BSD license.


## Compiling and linking against ADIOS

- `module load adios`

Adios is available in both serial and parallel versions.

Configuration details for ADIOS are obtained via the utility
`adios_config` which is available in the `PATH` once the
`adios` module is loaded. For example, to recover the compiler
options required to provide serial C include files, issue:
```
$ adios_config -s -c
```
Use `adios_config --help` for a summary of options.

To compile and link applciation, such statements can be embedded in a
Makefile via, e.g., 
```
ADIOS_INC := $(shell adios_config -s -c)
ADIOS_CLIB := $(shell adios_config -s -l)
```
See the ADIOS user manual for further details and examples.

The `adios` module defines the environment variable `ADIOS_DIR`
which will be appropriate for the current programming environment
when the `adios` module is loaded.


### Version history

=== "Full system"
    
    - Module `adios/1.13.1` installed October 2021 (PE 21.04)
    
=== "4-cabinet system"
    
    - Module `adios/1.13.1` installed January 2021


## Compile your own version

The Archer2 github repository provides a script which can be used to
build ADIOS as for the currently supported version, e.g.,:
```
$ git clone https://github.com/ARCHER2-HPC/pe-scripts.git
$ cd pe-scripts
$ git checkout modules-2021-10
$ module load cray-hdf5-parallel
$ ./sh/adios.sh --prefix=/path/to/install/location
```
where the `--prefix` option determines the install location. See the Archer2
[github repository](https://github.com/ARCHER2-HPC/pe-scripts/tree/cse-develop)
for further details and options.


## Resources

The ADIOS [home page](https://csmd.ornl.gov/adios)

ADIOS [user manual](http://users.nccs.gov/~pnorbert/ADIOS-UsersManual-1.10.0.pdf) (v1.10 pdf version)

ADIOS 1.x [github repository](https://github.com/ornladios/ADIOS)

