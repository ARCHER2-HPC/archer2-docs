# SLEPC

The Scalable Library for Eigenvalue Problem computations is an extension
of PETSc developed at the Universitat Politecnica de Valencia.  SLEPc is
freely available under a 2-clause BSD license.

## Compiling and linking with SLEPc

- `module load slepc`

To compile an application against the SLEPc libaries, load the `slepc`
module and use the compiler wrappers `cc`, `CC`, and `ftn` in the
usual way. Static libraries are available so no module is required at
run time.

The SLEPc module defines `SLEPC_DIR` which locates the root of the
installation.

### Version history

=== "Full system"
    
    - Module `slepc/3.14.1` installed October 2021 (PE 21.04)
    
=== "4-cabinet system"
    
    - Module `slepc/3.13.2` installed January 2021


## Compiling your own version

The version of SLEPc currently available on ARCHER2 can be compiled
using a script available from the ARCHER2 github repository:
```
$ git clone https://github.com/ARCHER2-HPC/pe-scripts.git
$ cd pe-scripts
$ git checkout modules-2021-10
$ ./sh/slepc.sh --prefix=/path/to/install/location
```
The dependencies (including PETSc) can be built in the same way,
or taken from the existing modules. See the ARCHER2
[github repository][1] for further information.

[1]: https://github.com/ARCHER2-HPC/pe-scripts/tree/cse-develop


## Resources

[SLEPc home page][10]

[SLEPc user manual (PDF)][12]

[SLEPc Gitlab repository][14]

[10]: https://slepc.upv.es 

[12]: https://slepc.upv.es/documentation/slepc.pdf

[14]: https://gitlab.com/slepc/slepc
