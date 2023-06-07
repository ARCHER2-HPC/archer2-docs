# ADIOS

The Adaptable I/O System (ADIOS) is developed at Oak Ridge National
Laboratory and is freely available under a BSD license.


## Version history

=== "Upgrade 2023"

The central installation of ADIOS (version 1) has been removed
as it is no longer actively developed.
A central installation of ADIOS (version 2) will be considered
as a replacement.

=== "Full system"

    - Module `adios/1.13.1` installed October 2021 (PE 21.04)

=== "4-cabinet system"

    - Module `adios/1.13.1` installed January 2021


## Compile your own version

The Archer2 github repository provides a script which can be used to
build ADIOS (version 1), e.g.,:
```
$ git clone https://github.com/ARCHER2-HPC/pe-scripts.git
$ cd pe-scripts
$ git checkout modules-2022-12
$ module load cray-hdf5-parallel
$ ./sh/adios.sh --prefix=/path/to/install/location
```
where the `--prefix` option determines the install location. See the Archer2
[github repository](https://github.com/ARCHER2-HPC/pe-scripts/tree/cse-develop)
for further details and options.

### Using ADIOS

Configuration details for ADIOS are obtained via the utility
`adios_config` which should be available in the `PATH` once
ADIOS is installed. For example, to recover the compiler
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


## Resources

The ADIOS [home page](https://csmd.ornl.gov/adios)

ADIOS [user manual](http://users.nccs.gov/~pnorbert/ADIOS-UsersManual-1.10.0.pdf) (v1.10 pdf version)

ADIOS 1.x [github repository](https://github.com/ornladios/ADIOS)
