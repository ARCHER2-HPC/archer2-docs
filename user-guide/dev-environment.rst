Application development environment
===================================

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

What's available
----------------

ARCHER2 runs on the Cray Linux Environment (a version of SUSE Linux),
and provides a development environment which includes:

* Software modules via a standard module framework
* Three different compiler environments (AMD, Cray, and GNU)
* MPI, OpenMP, and SHMEM
* Scientific and numerical libraries
* Parallel Python and R
* Parallel debugging and profiling
* Singularity containers

Access to particular software, and particular versions, is managed by
a standard TCL module framework. Most software is available via standard
software modules and the different programming environments are available
via module collections.

You can see what programming environments are available with:

::

  auser@uan01:~> module savelist
  Named collection list:
   1) PrgEnv-aocc   2) PrgEnv-cray   3) PrgEnv-gnu 


Other software modules can be listed with

::

  auser@uan01:~> module avail
  ------------------------------- /opt/cray/pe/perftools/20.09.0/modulefiles --------------------------------
  perftools       perftools-lite-events  perftools-lite-hbm    perftools-nwpc     
  perftools-lite  perftools-lite-gpu     perftools-lite-loops  perftools-preload  

  ---------------------------------- /opt/cray/pe/craype/2.7.0/modulefiles ----------------------------------
  craype-hugepages1G  craype-hugepages8M   craype-hugepages128M  craype-network-ofi          
  craype-hugepages2G  craype-hugepages16M  craype-hugepages256M  craype-network-slingshot10  
  craype-hugepages2M  craype-hugepages32M  craype-hugepages512M  craype-x86-rome             
  craype-hugepages4M  craype-hugepages64M  craype-network-none   

  ------------------------------------- /usr/local/Modules/modulefiles --------------------------------------
  dot  module-git  module-info  modules  null  use.own  

  -------------------------------------- /opt/cray/pe/cpe-prgenv/7.0.0 --------------------------------------
  cpe-aocc  cpe-cray  cpe-gnu  

  -------------------------------------------- /opt/modulefiles ---------------------------------------------
  aocc/2.1.0.3(default)  cray-R/4.0.2.0(default)  gcc/8.1.0  gcc/9.3.0  gcc/10.1.0(default)  
                                    

  ---------------------------------------- /opt/cray/pe/modulefiles -----------------------------------------
  atp/3.7.4(default)              cray-mpich-abi/8.0.15             craype-dl-plugin-py3/20.06.1(default)  
  cce/10.0.3(default)             cray-mpich-ucx/8.0.15             craype/2.7.0(default)                  
  cray-ccdb/4.7.1(default)        cray-mpich/8.0.15(default)        craypkg-gen/1.3.10(default)            
  cray-cti/2.7.3(default)         cray-netcdf-hdf5parallel/4.7.4.0  gdb4hpc/4.7.3(default)                 
  cray-dsmml/0.1.2(default)       cray-netcdf/4.7.4.0               iobuf/2.0.10(default)                  
  cray-fftw/3.3.8.7(default)      cray-openshmemx/11.1.1(default)   papi/6.0.0.2(default)                  
  cray-ga/5.7.0.3                 cray-parallel-netcdf/1.12.1.0     perftools-base/20.09.0(default)        
  cray-hdf5-parallel/1.12.0.0     cray-pmi-lib/6.0.6(default)       valgrind4hpc/2.7.2(default)            
  cray-hdf5/1.12.0.0              cray-pmi/6.0.6(default)           
  cray-libsci/20.08.1.2(default)  cray-python/3.8.5.0(default)      

A full discussion of the module system is available in
:doc:`sw-environment`.

.. TODO: Review if and when the TCL module system is updated

A consistent set of modules is loaded on login to the machine (currently
``PrgEnv-cray``, see below). Developing applications then means selecting
and loading the appropriate set of modules before starting work.

This section is aimed at code developers and will concentrate on the
compilation environment and building libraries and executables,
and specifically parallel executables. Other topics such as :doc:`python`
and :doc:`containers` are covered in more detail in separate sections of the documentation.

Managing development
--------------------

ARCHER2 supports common revision control software such as ``git``.

Standard GNU autoconf tools are available, along with ``make`` (which
is GNU Make). Versions of ``cmake`` are available.

Note that some of these tools are part of the system software, and
typically reside in ``/usr/bin``, while others are provided as part
of the module system. Some tools may be available in different versions
via both ``/usr/bin`` and via the module system.

.. TODO: Add in note on compiling in queues once this is available


Compilation environment
-----------------------

There are three different compiler environments available on ARCHER2:
AMD, Cray, and GNU. The current compiler suite is selected via the
programming environment, while the specific compiler versions are
determined by the relevant compiler module. A summary is:

================== ====================================== ====================
  PrgEnv module     Compiler Suite                         Compiler module
================== ====================================== ====================
  ``PrgEnv-aocc``   AMD Optimising C/C++ Compiler           ``aocc``             
  ``PrgEnv-cray``   Cray Compiler Environment               ``cce``           
  ``PrgEnv-gnu``    Gnu Compiler Collection                 ``gcc``          
================== ====================================== ====================

For example, at login, the default set of modules are:

.. TODO: Subject to periodic review the following...

::
  
  Currently Loaded Modulefiles:
  1) cpe-cray                          7) cray-dsmml/0.1.2(default)                           
  2) cce/10.0.3(default)               8) perftools-base/20.09.0(default)                     
  3) craype/2.7.0(default)             9) xpmem/2.2.35-7.0.1.0_1.3__gd50fabf.shasta(default)  
  4) craype-x86-rome                  10) cray-mpich/8.0.15(default)                          
  5) libfabric/1.11.0.0.233(default)  11) cray-libsci/20.08.1.2(default)                      
  6) craype-network-ofi  

from which we see the default programming environment is Cray (indicated
by ``cpe-cray`` (at 1 in the list above) and the default compiler module is ``cce/10.0.3``
(at 2 in the list above). The programming environment will give access to a consistent set
of compiler, MPI library via ``cray-mpich`` (at 10), and other libraries e.g.,
``cray-libsci`` (at 11 in the list above) infrastructure.

Within a given programming environment, it is possible to swap to
a different compiler version by swapping the relevant compiler
module.

.. TODO: How to swap to a consistent set of modules (when becomes available)

To ensure consistent behaviour, compilation of C, C++, and Fortran source
code should then take place
using the appropriate compiler wrapper: ``cc``, ``CC``, and ``ftn``,
respectively. The wrapper will automatically call the relevant
underlying compiler and add the appropriate include directories and
library locations to the invocation. This typically eliminates the need
to specify this additional information explicitly in the configuration
stage. To see the details of the exact compiler invocation use the
``-craype-verbose`` flag to the compiler wrapper.

The default link time behaviour is also related to the current programming
environment. See the section below on `Linking and libraries`_.

Users should not, in general, invoke specific compilers at compile/link
stages. In particular, ``gcc``, which may default to ``/usr/bin/gcc``,
should not be used. The compiler wrappers ``cc``, ``CC``, and ``ftn``
should be used via the appropriate module. Other common MPI compiler wrappers
e.g., ``mpicc`` should also be replaced by the relevant wrapper ``cc``
(``mpicc`` etc are not available).

.. important::

  Always use the compiler wrappers ``cc``, ``CC``, and/or ``ftn`` and not
  a specific compiler invocation. This will
  ensure consistent compile/link time behaviour.

Compiler man pages and help
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Further information on both the compiler wrappers, and the individual
compilers themselves are available via the command line, and via standard
``man`` pages. The ``man`` page for the compiler wrappers is common to
all programming environments, while the ``man`` page for individual
compilers depends on the currently loaded programming environment. The
following table summarises options for obtaining information on the
compiler and compiler options:

+----------+---------+--------------+-------------------+---------------------+
| PrgEnv   | Compiler| Wrapper      | Compiler          | Compiler options    |
|          |         | man page     | man page          | summary             |
+----------+---------+--------------+-------------------+---------------------+
| AOCC     |         |              |  TBC              |  ``clang --help``   |
+----------+         |              +-------------------+---------------------+
| Cray     | C       | ``man cc``   |  ``man craycc``   |  ``clang --help``   |
+----------+         |              +-------------------+---------------------+
| Gnu      |         |              |  ``man gcc``      |  ``gcc --help``     |
+----------+---------+--------------+-------------------+---------------------+
| AOCC     |         |              | TBC               | ``clang++ --help``  |
+----------+         |              +-------------------+---------------------+
| Cray     | C++     | ``man CC``   | ``man crayCC``    | ``clang++ --help``  |
+----------+         |              +-------------------+---------------------+
| Gnu      |         |              | ``man g++``       |  ``g++ --help``     |
+----------+---------+--------------+-------------------+---------------------+
| AOCC     |         |              | TBC               | ``flang --help``    |
+----------+         |              +-------------------+---------------------+
| Cray     | Fortran | ``man ftn``  | ``man crayftn``   |``ftn --craype-help``|
+----------+         |              +-------------------+---------------------+
| Gnu      |         |              | ``man gfortran``  |``gfortran --help``  |
+----------+---------+--------------+-------------------+---------------------+

Note that Cray C/C++ is based on a clang front end and therefore supports
similar options to clang/gcc (``man clang`` is in fact  equivalent to
``man craycc``). Note that ``clang --help`` will produce a full summary of
options with Cray-specific options marked "Cray". The ``craycc`` man page
concentrates on these Cray extensions to the ``clang`` front end and does
not provide an exhaustive description of all ``clang`` options.


Which compiler environment?
~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you are unsure which compiler you should choose, we suggest the
starting point should be the GNU compiler collection (GCC, ``PrgEnv-gnu``); this is
perhaps the most commonly used by code developers, particularly in
the open source software domain. A portable, standard-conforming
code should (in principle) compile in any of the three programming
environments.

For users requiring specific compiler features, such as co-array Fortran,
the recommended starting point would be Cray. The following sections
provide further details of the different programming environments.

.. note::

  Intel compilers are not available on ARCHER2.

AMD Optimizing C/C++ Compiler (AOCC)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The AMD Optimizing C/++ Compiler (AOCC) is a clang-based optimising
compiler. AOCC (despite its name) includes a flang-based Fortran
compiler.

Switch the the AOCC programming environment via

::

  $ module restore PrgEnv-aocc

.. note::

  Further details on AOCC will appear here as they become available.


AOCC reference material
^^^^^^^^^^^^^^^^^^^^^^^

:AMD website: https://developer.amd.com/amd-aocc/


Cray compiler environment (CCE)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The Cray compiler environment (CCE) is the default compiler at the point
of login. CCE supports C/C++ (along with unified parallel C UPC), and
Fortran (including co-array Fortran). Support for OpenMP parallelism
is available for both C/C++ and Fortran (currently OpenMP 4.5, with
a number of exceptions).

The Cray C/C++ compiler is based on a clang front end, and so compiler
options are similar to those for gcc/clang. However, the Fortran
compiler remains based around Cray-specific options. Be sure to separate
C/C++ compiler options and Fortran compiler options (typically ``CFLAGS``
and ``FFLAGS``) if compiling mixed C/Fortran applications.

Switch the the Cray programming environment via

::

  $ module restore PrgEnv-cray

Useful CCE C/C++ options
^^^^^^^^^^^^^^^^^^^^^^^^

When using the compiler wrappers ``cc`` or ``CC``, some of the following
options may be useful:

+-----------------------------------------------------------------------------+
| Language, Warning, Debugging options                                        |
+----------------------+------------------------------------------------------+
| Option               |  Comment                                             |
+----------------------+------------------------------------------------------+
| ``-std=<standard>``  |  Default is ``-std=gnu11`` (``gnu++14`` for C++) [1] |
+----------------------+------------------------------------------------------+
| Performance options                                                         |
+----------------------+------------------------------------------------------+
| ``-Ofast``           | Optimisation levels: -O0, -O1, -O2, -O3, -Ofast      |
+----------------------+------------------------------------------------------+
| ``-ffp=level``       | Floating point maths optimisations levels 0-4 [2]    |
+----------------------+------------------------------------------------------+
| ``-flto``            | Link time optimisation                               |
+----------------------+------------------------------------------------------+
| Miscellaneous options                                                       |
+----------------------+------------------------------------------------------+
| ``-fopenmp``         | Compile OpenMP (default is off)                      |
+----------------------+------------------------------------------------------+
| ``-v``               | Display verbose output from compiler stages          |
+----------------------+------------------------------------------------------+

Notes:

1. Option ``-std=gnu11`` gives ``c11`` plus GNU extensions
   (likewise ``c++14`` plus GNU extensions).
   See https://gcc.gnu.org/onlinedocs/gcc-4.8.2/gcc/C-Extensions.html

2. Option ``-ffp=3`` is implied by ``-Ofast`` or ``-ffast-math``


Useful CCE Fortran options
^^^^^^^^^^^^^^^^^^^^^^^^^^

+-----------------------------------------------------------------------------+
| Language, Warning, Debugging options                                        |
+----------------------+------------------------------------------------------+
| Option               |  Comment                                             |
+----------------------+------------------------------------------------------+
| ``-m <level>``       | Message level (default ``-m 3`` errors and warnings) |
+----------------------+------------------------------------------------------+
| Performance options                                                         |
+----------------------+------------------------------------------------------+
| ``-O <level>``       | Optimisation levels: -O0 to -O3 (default -O2)        |
+----------------------+------------------------------------------------------+
| ``-h fp<level>``     | Floating point maths optimisations levels 0-3        |
+----------------------+------------------------------------------------------+
| ``-h ipa``           | Inter-procedural analysis                            |
+----------------------+------------------------------------------------------+
| Miscellaneous options                                                       |
+----------------------+------------------------------------------------------+
| ``-h omp``           | Compile OpenMP (default is ``-hnoomp``)              |
+----------------------+------------------------------------------------------+
| ``-v``               | Display verbose output from compiler stages          |
+----------------------+------------------------------------------------------+


GNU compiler collection (GCC)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The commonly used open source GNU compiler collection is available and
provides C/C++ and Fortran compilers.

The GNU compiler collection is loaded by switching to the GNU programming
environment:

::

  $ module restore PrgEnv-gnu

Reference material
^^^^^^^^^^^^^^^^^^

:C/C++ documentation: https://gcc.gnu.org/onlinedocs/gcc-9.3.0/gcc/

:Fortran documentation: https://gcc.gnu.org/onlinedocs/gcc-9.3.0/gfortran/

Message passing interface (MPI)
-------------------------------

MPICH
~~~~~

HPE Cray provide as standard an MPICH implementation of the message passing
interface which is specifically optimised for the ARCHER2 network. The
current implementation supports MPI standard version 3.1. This
implementation should be used wherever possible.

Useful environment variables associated with MPICH will be documented
here.

ABI compatibility. This may be useful in cases where a source distribution
is not available.


MPI Resource limits
^^^^^^^^^^^^^^^^^^^

Some common resource limits e.g., number of communicators, number
of message tags, will be documented here.

Fortran and the mpi module
^^^^^^^^^^^^^^^^^^^^^^^^^^


MPI reference material
^^^^^^^^^^^^^^^^^^^^^^

MPI standard documents: https://www.mpi-forum.org/docs/


Linking and libraries
---------------------

Linking to libraries is performed dynamically on ARCHER2. One can use
the ``-craype-verbose`` flag to the compiler wrapper to check
exactly what linker arguments are invoked. The compiler wrapper scripts
encode the paths to the programming environment system libraries using
the "runpath". This ensures that the executable can find the correct
runtime libraries without the matching software modules loaded.

The library runpath associated with an executable can be inspected
via, e.g.,

::

  $ readelf -d ./a.out


Commonly used libraries
~~~~~~~~~~~~~~~~~~~~~~~

Modules with names prefixed by ``cray-`` are provided by HPE Cray, and are
supported to be consistent with any of the programming environments
and associated compilers. These modules should be the first choice for
access to the following libraries.

HPE Cray LibSci
^^^^^^^^^^^^^^^

================== ======================================
  Library             Module
------------------ --------------------------------------
BLAS, LAPACK, ...     ``module load cray-libsci``
================== ======================================

Cray scientific libraries, available for all compiler choices
provides access to the Fortran BLAS_ and LAPACK_ interface for basic linear
algebra, the corresponding C interfaces CBLAS_ and LAPACKE_, and BLACS_ and
ScaLAPACK_ for parallel linear algebra.
Type ``man intro_libsci`` for further details.

.. _BLAS:       http://www.netlib.org/blas/
.. _LAPACK:     http://www.netlib.org/lapack/
.. _CBLAS:      http://www.netlib.org/blas/#_cblas
.. _LAPACKE:    https://www.netlib.org/lapack/lapacke.html
.. _BLACS:      https://www.netlib.org/blacs/
.. _ScaLAPACK:  http://www.netlib.org/scalapack/

FFTW
^^^^

================== ======================================
  Library             Module
------------------ --------------------------------------
FFTW                 ``module load cray-fftw``
================== ======================================

FFTW_ provides Fast Fourier Transforms, and is available in
version 3 only.


.. _FFTW:      http://www.fftw.org

HDF5
^^^^

================== ======================================
  Library             Module
------------------ --------------------------------------
 HDF5                 ``module load cray-hdf5``
 HDF5 Paralel         ``module load cray-hdf5-parallel``
================== ======================================


Hierarchical Data Format HDF5_ is available in serial and parallel
versions.

NetCDF
^^^^^^

================== ===========================================
  Library             Module
------------------ -------------------------------------------
 NetCDF              ``module load cray-netcdf``
 NetCDF + HDF5       ``module load cray-netcdf-hdf5parallel``
 PnetCDF             ``module load cray-parallel-netcdf``
================== ===========================================

Network Common Data Form NetCDF_, and parallel NetCDF (usually
referred to as PnetCDF_) are available.

A full description of the relationship between various HDF5 and NetCDF
options will appear here.

Reference material
^^^^^^^^^^^^^^^^^^

:HDF5:    https://portal.hdfgroup.org/display/HDF5/HDF5

:NetCDF:  https://www.unidata.ucar.edu/software/netcdf/

:PnetCDF: https://parallel-netcdf.github.io/wiki/Documentation.html

.. _HDF5:    https://portal.hdfgroup.org/display/HDF5/HDF5
.. _NetCDF:  https://www.unidata.ucar.edu/software/netcdf/
.. _PnetCDF: https://parallel-netcdf.github.io/wiki/Documentation.html



Building standard packages
--------------------------

The ARCHER2 team provide build configurations or instructions for a number of
standard libraries and software packages. Users wanting particular
versions of public packages, particularly development versions
which are not available centrally, are encouraged to try the build
configuration and consult the Service Desk if there are problems.

