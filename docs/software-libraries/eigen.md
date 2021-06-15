# Eigen

Eigen is a C++ template library for linear algebra: matrices,
vectors, numerical solvers, and related algorithms.

## Compiling with Eigen

- `module load eigen`

To compile an application with the Eigen header files, load the
`eigen` module and use the compiler wrappers `cc`, `CC`, or `ftn` in
the usual way. The relevant header files will be introduced
automatically.

The header files are located in `/work/y07/shared/libs/eigen/3.3.8/`,
and can be included manually at compilation without loading the module
if required.


### Version history

- Module `eigen/3.3.8` installed June 2021


## Compiling your own version

The current supported version on Archer2 can be built using the
following script
```
$ wget https://gitlab.com/libeigen/eigen/-/archive/3.3.8/eigen-3.3.8.tar.gz
$ tar xvf eigen-3.3.8.tar.gz
$ cmake eigen-3.3.8/ -DCMAKE_INSTALL_PREFIX=/path/to/install/location
$ make install
```
where the `-DCMAKE_INSTALL_PREFIX` option determines the install
directory. Installing in this way will also build the Eigen
documentation and unit-tests.

## Resources

Eigen [home page](https://eigen.tuxfamily.org/index.php?title=Main_Page)

[Getting Started guide](https://eigen.tuxfamily.org/dox/GettingStarted.html)
