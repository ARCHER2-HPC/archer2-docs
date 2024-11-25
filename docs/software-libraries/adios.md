# ADIOS

The Adaptable I/O System (ADIOS) is developed at Oak Ridge National
Laboratory and is freely available under a BSD license. The current
development is ADIOS2.


## Version history

=== "Current"

Versions of ADIOS2 for different programming environments are available.
See, e.g.:
```
user@ln01:> module load other-software
user@ln01:> module avail adios2
```
Please load the appropriate module for the current programming environment.

=== "Upgrade 2023"

The central installation of ADIOS (version 1) has been removed
as it is no longer actively developed.

=== "Full system"

    - Module `adios/1.13.1` installed October 2021 (PE 21.04)

=== "4-cabinet system"

    - Module `adios/1.13.1` installed January 2021


### Using ADIOS2

Configuration details for ADIOS2 are obtained via the utility
`adios2-config` which should be available in the `PATH` once
ADIOS is installed. For example, to recover the compiler
options required to provide serial C include files, issue:
```
$ adios2-config -s -c
```
Use `adios2-config --help` for a summary of options.

To compile and link application, such statements can be embedded in a
Makefile via, e.g.,
```
ADIOS_INC := $(shell adios2-config -s -c)
ADIOS_CLIB := $(shell adios2-config -s -l)
```
See the ADIOS2 user manual for further details and examples.

## Compile your own version

Details for ADIOS2 are pending.


## Resources

The ADIOS2 [user manual](https://adios2.readthedocs.io/en/)

The ADIOS2 [github repository](https://github.com/ornladios/ADIOS2)
