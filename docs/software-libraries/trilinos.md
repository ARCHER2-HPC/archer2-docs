# Trilinos

Trilinos is a large collection of packages with software components that
can be used
for scientific and engineering problems. Most of the package are
released under a BSD license (and some under LGPL).

## Compiling and linking against Trilinos

- `module load trilinos`

Applications may be built against the module version od Trilinos by
using the using the compiler wrappers `CC` or `ftn` in the normal
way. The appropriate include files and library paths will be
inserted automatically.

The `trilinos` module defines the environment variable `TRILINOS_DIR`
as the root of the installation for the current programming environment.

Trilinos also provides a small number of stand-alone execuables which
are available via the standard `PATH` mechanism while the module is
loaded.


### Version history

- `module trilinos/12.18.1` installed January 2021

    Packages enabled are: `Amesos, Amesos2, Anasazi, AztecOO Belos Epetra
    EpretExt FEI Galeri GlobiPack Ifpack Ifpack2 Intrepid
    Isorropia Kokkos Komplex Mesquite ML Moertel MueLu NOX
    OptiPack Pamgen Phalanx Piro Pliris ROL RTOp Rythmos Sacado Shards
    ShyHU STK STKSearch STKTopology STKUtil Stratimikos Teko Teuchos Thyra
    Tpetra TrilinosCouplings Triutils Xpetra Zoltan Zoltan2`

    Known issue: Trilinos is not available in `PrgEnv-aocc` at the moment.

    Known issue: The `ForTrilinos` package is not available in this version.

## Compiling Trilinos

A script which has details of the relevant configuration options
for Trilinos is available at the ARCHER2 repository. The script
will build a static-only version of the libraries.
```
$ git clone https://github.com/ARCHER2-HPC/pe-scripts.git
$ cd pe-scripts
$ git checkout cse-develop
$ ...
$ ./sh/trilinos.sh --prefix=/path/to/install/location
```
where `--prefix` sets the installation location. The ellipsis `...`
is standing for the dependencies used to build Trilinos, which
here are: `metis, parmetis, superlu, superlu-dist, scotch, mumps,
glm, boost`. These packages should be built as described in their
corresponding pages linked in the menu on the left.

See the ARCHER2 [github repository][2] for further details.

Note that Trilinos may take up to one hour to compile on its own, and
so the compilation is best performed as a batch job.

[2]: https://github.com/ARCHER2-HPC/pe-scripts/tree/cse-develop


## Resources


Trilinos [home page](https://trilinos.github.io)

Trilinos [github repoistory](https://github.com/trilinos/Trilinos)
