Application development environment
===================================

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

What's available
----------------

ARCHER2 runs on the Cray Linux Environment (a version of SUSU Linux),
and provides a Cray Developer Environment  (CDE) which includes:

* Software modules via a standard module framework
* Three different compiler environments (AMD, Cray, and GNU)
* MPI, OpenMP, and SHMEM
* Scientific and numerical libraries
* Parallel Python and R
* Parallel debugging and profiling
* SLURM scheduler and job management
* Singularity containers

Access to particular software, and particular versions, is managed via
the module framework. For a broad overview, a full list of what's
available can be seen by typing

::

  $ module avail

A full discussion of the module system is available in
:doc:`sw-environment`.


.. warning::

  The following statement needs to be confirmed

By default, all users on ARCHER2 start with no modules loaded. Developing
applications then means selecting and loading an appropriate set of
modules before starting work.

This section is aimed at code developers and will concentrate on the
compilation environment and building executables, and specifically
parallel executables. Other topics such as :doc:`python`
and :doc:`containers` are
covered in more detail in separate sections of the documentation.

Managing development
--------------------

ARCHER2 supports common revision control software such as ``git``, ``svn``,
and ``mercurial``.

Standard GNU autoconf tools are available, along with ``make`` (which
is GNU Make). Versions of ``cmake`` are avilable.

Compilation via the queue system
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Build jobs which take considerable time, or use considerable
resources (via e.g., ``make -j``), should be considered candidates
for the queue system.

As the hardware and software environment on both the login nodes and
the back end nodes is uniform on ARCHER2, there are no special
considerations for building applications via the queue system.


Compilation Environment
-----------------------

There are three different compiler environments avialable: AMD, Cray,
and GNU. These are accessed by loading the appropriate programming
environment module, i.e., exactly one of:

::

  $ module load PrgEnv-aocc
  $ module load PrgEnv-cray
  $ module load PrgEnv-gnu

This will give access to a consistent set of compiler and message passing
interface (MPI) intrastructure.

Compilation of C, C++, and Fortran soruce code should then take place
using the appropriate compiler wrapper: ``cc``, ``CC``, and ``ftn``,
respectively. The wrapper will automatically call the relevant
underlying compiler and add the appropriate include directories and
library locations to the invocation. This typically eliminates the need
to specify this additional information explicitly in the configuration
stage.

Users should not, in general, invoke specific compilers. In particular,
``gcc``, which may default to ``/usr/bin/gcc``, should not be used.
The compiler wrappers ``cc``, ``CC``, and ``ftn`` should be used via
the appropriate module.

However, one exception is if specific ``man`` pages are required. The
command ``man cc`` etc will provide the man page on the compiler
wrapper. Specific compiler man pages are invoked via the underlying
compiler name, which are given in the relevant sections below.


General remarks on compilation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

All Cray-provided modules will automatically be compatible with
the appropriate compiler environment. E.g., to use NetCDF

::

  module load PrgEnv-cray
  module load cray-netcdf

and use the appropriate compiler wrapper to ensure the correct header
files and modules can be located at compile time, and the correct
libraries at link time.

.. note::

  Further general remarks on compilation will appear here.

.. note::

   As ARCHER2 uses dynamic linking by default you will generally also need
   to load any modules you used to compile your code in your job submission
   script when you run your code.


Which compiler environment?
~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you are unsure which compiler you should choose, we suggest the
starting point should be the GNU compiler collection; this is
probably the most commonly used by code developers.

For users requiring specific compiler features, such as co-array Fortran,
the recommended starting point would be Cray.



AMD Optimizing Compiler Collection (AOCC)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Cray Compiler Environment (CCE)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

GNU Compiler Collection (GCC)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The GNU compiler collection is loaded via

::

  $ module load PrgEnv-gnu

Full details of behaviour of the compilers can be seen on individual man pages
via ``man gcc``, ``man g++`` or ``man gfortran``. Here we provide a summary
of a number of useful options.

=============     ============   ==========================================
Feature           Option         Comments
=============     ============   ==========================================
Enable OpenMP     ``-fopenmp``   Must be used at both compile and link time
=============     ============   ==========================================




Message passing interface (MPI)
-------------------------------

MPICH
~~~~~

Cray provide as standard an MPICH implementation of the message passing
interface which is specifically optimised for the ARCHER2 network. This
implementation should be used wherever possibile.

Some common resource limits e.g., number of communicators, number
of message tags, will be documented here.

Useful environment variables assoicated with MPICH will be documented
here.

ABI compatability. This may be useful in cases where a source distribution
is not available.


Using a different MPI
~~~~~~~~~~~~~~~~~~~~~

.. note::

  Details will appear here (if this is possible)


Linking and Libraries
---------------------


Using static linking
~~~~~~~~~~~~~~~~~~~~

By default, executables on ARCHER2 are built using shared/dynamic libraries 
(that is, libraries which are loaded at run-time as and when
needed by the application) when using the wrapper scripts. 

An application compiled this way to use shared/dynamic libraries will
use the default version of the library installed on the system (just
like any other Linux executable), even if the system modules were set
differently at compile time. This means that the application may
potentially be using slightly different object code each time the
application runs as the defaults may change. This is usually the desired
behaviour for many applications as any fixes or improvements to the
default linked libraries are used without having to recompile the
application, however some users may feel this is not the desired
behaviour for their applications.

Alternatively, applications can be compiled to use static
libraries (i.e. all of the object code of referenced libraries are
contained in the executable file).  This has the advantage
that once an executable is created, whenever it is run in the future, it
will always use the same object code (within the limit of changing runtime 
environemnt). However, executables compiled with static libraries have
the potential disadvantage that when multiple instances are running
simultaneously multiple copies of the libraries used are held in memory.
This can lead to large amounts of memory being used to hold the
executable and not application data.

To create an application that uses static libraries you must
pass an extra flag during compilation, ``-Bstatic``.

Use the UNIX command ``ldd exe_file`` to check whether you are using an
executable that depends on shared libraries. This utility will also
report the shared libraries this executable will use if it has been
dynamically linked.


Commonly used libraries
~~~~~~~~~~~~~~~~~~~~~~~

Cray scientific libraries, available for all compiler choices via

::

  module load cray-libsci

provides access to the Fortran BLAS_ and LAPACK_ interface for basic linear
algebra, the corresponding C interfaces CBLAS_ and LAPACKE_, and BLACS_ and
ScaLAPACK_ for parallel linear algebra.

.. _BLAS:       http://www.netlib.org/blas/
.. _LAPACK:     http://www.netlib.org/lapack/
.. _CBLAS:      http://www.netlib.org/blas/#_cblas
.. _LAPACKE:    https://www.netlib.org/lapack/lapacke.html
.. _BLACS:      https://www.netlib.org/blacs/
.. _ScaLAPACK:  http://www.netlib.org/scalapack/


FFTW_ provides Fast Fourrier Transforms, and is available via

::

  module load fftw3

Note that FFTW Version 2 is no longer supported. Developers are strongly
advised to move to FFTW Version 3.

.. _FFTW:      http://www.fftw.org

Hierarchical Data Format HDF5_ is available via

::

   $ module load hdf


Network Common Data Form NetCDF_ is available via

::

  $ module load netcdf

A full description of the relationship between various HDF5 and NetCDF
options will appear here.

.. _HDF5:    https://portal.hdfgroup.org/display/HDF5/HDF5
.. _NetCDF:  https://www.unidata.ucar.edu/software/netcdf/


Building standard packages
--------------------------

The ARCHER2 team provide Build configurations for a number of
standard libraries and software packages. Users wanting particular
versions of public packages, particularly development versions
which are not available centrally, are encourage to try the Build
configuration and consult the Service Desk if there are problems.

.. note::

  Full details will appear as they become avaialble