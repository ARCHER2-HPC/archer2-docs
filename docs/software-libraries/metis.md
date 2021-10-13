# Metis and Parmetis

The University of Minnesota provide a family of libraries for
partitioning graphs and meshes, and computing fill-reducing ordering
of sparse matrices. These libraries coming broadly under the label of
"Metis". They are free to use for educational and research purposes.

## Metis

  - `module load metis`

Metis is the sequential library for partitioning problems; it also
supplies a number of simple stand-alone utility programs to access the Metis
API for graph and mesh partioning, and graph and mesh manipulation. The
stand alone programs typically read a graph or mesh from file which must
be in "metis" format.

### Compiling and linking with Metis

The Metis library available via `module load metis` comes both with and
without support for OpenMP. When using the compiler wrappers `cc`, `CC`,
and `ftn`, the appropriate version will be selected based on the presence or
absence of, e.g., `-fopenmp` in the compile or link invocation.

Use, e.g.,
```
$ cc --cray-print-opts
```
or
```
$ cc -fopenmp --cray-print-opts
```
to see exactly what options are being issued by the compiler wrapper
when the `metis` module is loaded.

Metis is currently provided as static libraries, so it should not
be necessary to re-load the `metis` module at run time.

The serial utilities (e.g. `gpmetis` for graph partitioning) are
supplied without OpenMP. These may then be run on the front end for
small problems if the `metis` module is loaded.

The `metis` module defines the environment variable `METIS_DIR` which
indicates the current location of the Metis installation.


## Parmetis

  - `module load parmetis`

Parmetis is the distributed memory incarnation of the Metis functionality.
As for the `metis` module, Parmetis is integrated with use of the compiler
wrappers `cc`, `CC`, and `ftn`.

Parmetis depends on the `metis` module, which is loaded automatically by
the `parmetis` module.

The `parmetis` module defines the environment variable `PARMETIS_DIR` which
holds the current location of the Parmetis installation. This
variable may not respond to a change of compiler version within a
given programming environment. If you wish to use `PARMETIS_DIR`
in such a context, you may need to (re-)load the `parmetis` module
after the change of compiler version.


### Module version history

=== "Full system"
    
    - module `metis/5.1.0` installed October 2021 (PE21.04)
    - module `parmetis/4.0.3` installed January 2021 (PE21.04)
    
=== "4-cabinet system"
    
    - module `metis/5.1.0` installed January 2021
    - module `parmetis/4.0.3` installed January 2021

## Compile your own version

The build procedure used for the Metis and Parmetis libraries on Archer2
is available via github.

### Metis

The latest Archer2 version of Metis can be installed

```
$ git clone https://github.com/ARCHER2-HPC/pe-scripts.git
$ cd pe-scripts
$ git checkout modules-2021-10
$ ./sh/tpsl/metis.sh --prefix=/path/to/install/location
```

where `--prefix` determines the install location. This will download
and install the default version for the current programming environment.

### Parmetis

Parmetis can be installed in via the same mechanism as Metis:
```
$ ./sh/tpsl/parmetis.sh --prefix=/path/to/install/location
```
The Metis package should be installed first (as above) using the same
location. See the
[Archer2 repository](https://github.com/ARCHER2-HPC/pe-scripts/tree/cse-develop)
for further details and options.

## Resources

  - [Metis at George Karypis' site](http://glaros.dtc.umn.edu/gkhome/views/metis)

  - [Metis homepage](http://glaros.dtc.umn.edu/gkhome/views/metis/overview)
  - [Metis manual (pdf)](http://glaros.dtc.umn.edu/gkhome/fetch/sw/metis/manual.pdf)

  - [Parmetis homepage](http://glaros.dtc.umn.edu/gkhome/metis/parmetis/overview)
  - [Parmetis manual (pdf)](http://glaros.dtc.umn.edu/gkhome/fetch/sw/parmetis/manual.pdf)
