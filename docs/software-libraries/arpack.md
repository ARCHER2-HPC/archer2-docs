# ARPACK-NG

The Arnoldi Package (ARPACK) was designed to compute eigenvalues
and eigenvectors of large sparse matrices. Originally from Rice
University, an open source version (ARPACK-NG) is available under
a BSD license and is made available here.


## Compiling and linking with ARPACK

- `module load arpack-ng`

To compile an application against the ARPACK-NG libraries, load the `arpack-ng`
module and use the compiler wrappers `cc`, `CC`, and `ftn` in the
usual way.

The `arpack-ng` module defines `ARPACK_NG_DIR` which locates the root of the
installation for the current programming environment.


### Version history

=== "Full system"
    
    - Module `arpack-ng/3.8.0` installed October 2021 (PE 21.04)
    
=== "4-cabinet system"
    
    - Not available


## Compiling your own version

The current supported version of MUMPS on Archer2 can be compiled using
a script available from the Archer githug repository.
```
$ git clone https://github.com/ARCHER2-HPC/pe-scripts.git
$ cd pe-scripts
$ git checkout modules-2021-10
$ ./sh/arpack-ng.sh --prefix=/path/to/install/location
```
where the `--prefix` specifies a suitable location. See the
Archer2 [github repository](https://github.com/ARCHER2-HPC/pe-scripts/tree/cse-develop)
for further options and details.


## Resources

[Original ARPACK page](https://www.caam.rice.edu/software/ARPACK/)

[ARPACK-NG github site](https://github.com/opencollab/arpack-ng/)
