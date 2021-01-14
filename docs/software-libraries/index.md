# Software Libraries

This section provides information on centrally-installed software
libraries and library-based packages. These provide significant
functionality that is of interest to both users and developers of
applications.

Libraries are made available via the module system, and fall into
a number of distinct groups.

## Libraries via modules `cray-*`

The following libraries are available as modules prefixed by `cray-`
and may be of direct interest to developers and users. The modules are
provided by HPE Cray to be optimised for performance on the Archer2
hardware, and should be used where possible. The relevant
modules are:

- **cray-fftw** [*...details for module load cray-fttw...*](fftw.md)

    FFTW (Fastest Fourier Transform in the West) is a standard package for
    discrete Fourier tranforms. See the
    [FFTW home page http://www.fftw.org/][1]

- **cray-ga**

    Global Arrays (GA) provides a partitioned global address space (PGAS)
    programming model used by a number of applications. See the
    [GA home page at https://hpc.pnl.gov/globalarrays/index.shtml][2]

- **cray-hdf5** and **cray-hdf5-parallel** [*...details for hdf5...*](hdf5.md)

    Hierarchical Data Format (HDF5) is a high-performance and portable data
    format and data model. These modules provide serial and parallel
    varients of HDF5.
    https://portal.hdfgroup.org/display/HDF5/HDF5

- **cray-libsci** [*...details for cray-libsci...*](libsci.md)

    
     BLAS, LAPACK, BLACS, and SCALAPACK provide basic linear algebra
     functionality such as vector-vector, matrix-vector, and
     matrix-matrix multiplaition.
     Module `cray-libsci` is loaded by default in all programming
     environments.

- **cray-netcdf** [*...details for NetCDF...*](netcdf.md)

    Serial version of Network Common Data Form (NetCDF), a widely used
    and portable data format.
    See https://www.unidata.ucar.edu/software/netcdf/

- **cray-netcdf-hdf5parallel**

    A serial NetCDF built against parallel HDF5.
 
- **cray-parallel-netcdf**

    A parallel NetCDF implementation (sometimes referred to as "Pnetcdf").

[1]: https://hpc.pnl.gov/globalarrays/index.shtml
[2]: http://www.fftw.org/


### Integration with compiler environment

All libraries provided by  modules prefixed `cray-` integrate with the
compiler environment, and so appropriate compiler and link stage options
are injected when using the standard compiler wrappers `cc` `CC` and `ftn`.


## Libraries supported by Archer CSE team

The following libraries will also made available by the Archer service:

- **ADIOS** [*...details for ADIOS on Archer2...*](adios.md)

    ADIOS (Adaptable I/O System) provides library services for parallel I/O. 

- **Boost** [*...details for Boost on Archer2...*](boost.md)

    Boost is a partable C++ library providing reference implementations
    of many common containers, operations and algorithms.

- **GLM** [*...details for GLM on Archer2...*](glm.md)

    GLM (GL Math library) is a C++ header-only library for performing
    operations commonly encountered in graphics applications.
    See 

- **Hypre** [*...details for HYPRE on Archer2...*](hypre.md)

    HYPRE provides pre-conditioners and solvers for sparse linear algebra
    problems. See https://hypre.readthedocs.io/en/latest/ch-intro.html

- **Metis** and **Parmetis** [*...details for Metis and Parmetis...*](metis.md)

    [METIS][500] is a set of (serial) routines for partitioning graphs and
    meshes, and computing reduced-fill orderings of sparse matrices. It is
    commonly used e.g., to compute decompositions for finite element problems.
    [Parmetis][501] is the distributed memory counterpart.

[500]: http://glaros.dtc.umn.edu/gkhome/metis/metis
[501]: http://glaros.dtc.umn.edu/gkhome/metis/parmetis/overview

- **Mumps** [*...details for MUMPS on Archer2...*](mumps.md)

    MUMPS provides parallel direct solution of large sparse matrix problems.
    See http://mumps.enseeiht.fr

- **PETSc** [*...details for PETSc on Archer2...*](petsc.md)

    [PETSc][700] is a general package with functionality related to the
    solution of a wide range of problems described by partial differential
    equations.

[700]:  https://www.mcs.anl.gov/petsc/

- **Scotch** [*...details for Scotch and PT-Scotch on Archer2...*](scotch.md)

    Scotch (and its parallel partner PT-Scotch) is a graph partioning library.
    https://www.labri.fr/perso/pelegrin/scotch/

- **SLEPC** *...details pending...*

    SLEPC is a package for large eigenvalue problems based on PETSc.
    See https://slepc.upv.es

- **SUNDIALS** *...details pending...*

    Sundials is a suite of libraries which address problems including
    ordinary differential equation integration, initial value problems,
    and non-linear algebraic equations.
    See https://computing.llnl.gov/projects/sundials

- **SuperLU** and **SuperLU_DIST** [*...details for SuperLU on Archer2...*](superlu.md)

    [SuperLU][800] provides solutions to large non-symmetric sparse systems.
    [SuperLU_DIST][810] is the distributed memory version.

[800]: https://portal.nersc.gov/project/sparse/superlu/
[810]: https://portal.nersc.gov/project/sparse/superlu/#superlu_dist

- **Trilinos** [*...details for Trilinos on Archer2...*](trilinos.md)

    Trilinos is a large collection of packages for the solution of complex
    scientific and engineering problems.
    See e.g., [https://trilinos.github.io/](https://trilinos.github.io/)


### Integration with compiler environment

Again, all the libraries listed above are supported by all programming
environments via the module system. Additional compile and link time
flags should not be required.


### Building your own library versions

For the libraries listed in this section, a set of build and installation
scripts are available at
https://github.com/ARCHER2-HPC/pe-scripts/tree/cse-develop

Follow the instructions to build the relevant package (note this
is the `cse-develop` branch of the repoistory). See also individual
libraries pages in the list above for further details.

The scripts available from this repoistory should work in all three
programming environments.
