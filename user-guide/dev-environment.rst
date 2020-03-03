Application development environment
===================================

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

The application development environment on ARCHER2 is primarily
controlled through the *modules* environment. By loading and switching
modules you control the compilers, libraries and software available.

This means that for compiling on ARCHER2 you typically set the compiler
you wish to use using the appropriate modules, then load all the
required library modules (e.g. numerical libraries, IO format libraries).

Additionally, if you are compiling parallel applications using MPI 
(or SHMEM, etc.) then you will need to load one of the MPI environments
and use the appropriate compiler wrapper scripts.

By default, all users on ARCHER2 start with no modules loaded.

Basic usage of the ``module`` command on ARCHER2 is covered below. For
full documentation please see:

-  `Linux manual page on modules <http://linux.die.net/man/1/module>`__

Using the modules environment
-----------------------------

Information on the available modules
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Finding out which modules (and hence which compilers, libraries and
software) are available on the system is performed using the
``module avail`` command:

::

    [user@archer2-login0 ~]$ module avail
    ...

This will list all the names and versions of the modules available on
the service. Not all of them may work in your account though due to,
for example, licencing restrictions. You will notice that for many
modules we have more than one version, each of which is identified by a
version number. One of these versions is the default. As the
service develops the default version will change.

You can list all the modules of a particular type by providing an
argument to the ``module avail`` command. For example, to list all
available versions of the Intel Compiler type:

::

    [user@archer2-login0 ~]$ module avail intel-compilers
 
    --------------------------------------- /lustre/sw/modulefiles ---------------------------------------
    intel-compilers-16/16.0.2.181 intel-compilers-16/16.0.3.210

If you want more info on any of the modules, you can use the
``module help`` command:

::

    [user@archer2-login0 ~]$ module help mpt

    ----------- Module Specific Help for 'mpt/2.14' -------------------

    The HPE Message Passing Toolkit (MPT) is an optimized MPI
    implementation for HPE systems and clusters.  See the
    MPI(1) man page and the MPT User's Guide for more
    information.

The simple ``module list`` command will give the names of the modules
and their versions you have presently loaded in your envionment:

::

    [user@archer2-login0 ~]$ module list
    Currently Loaded Modulefiles:
    1) mpt/2.14                        3) intel-fc-16/16.0.3.210
    2) intel-cc-16/16.0.3.210          4) intel-compilers-16/16.0.3.210

Loading, unloading and swapping modules
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To load a module to use ``module add`` or ``module load``. For example,
to load the intel-compilers-17 into the development environment:

::

    module load intel-compilers-17

This will load the default version of the intel compilers. If
you need a specfic version of the module, you can add more information:

::

    module load intel-compilers-17/17.0.2.174

will load version 17.0.2.174 for you, regardless of the default. If you
want to clean up, ``module remove`` will remove a loaded module:

::

    module remove intel-compilers-17

(or ``module rm intel-compilers-17`` or
``module unload intel-compilers-17``) will unload what ever version of
intel-compilers-17 (even if it is not the default) you might have
loaded. There are many situations in which you might want to change the
presently loaded version to a different one, such as trying the latest
version which is not yet the default or using a legacy version to keep
compatibility with old data. This can be achieved most easily by using 
"module swap oldmodule newmodule". 

Suppose you have loaded version 16.0.2.181, say, of intel-compilers-16, the following command will change to version 16.0.3.210:

::

    module swap intel-compilers-16 intel-compilers-16/16.0.2.181

Modules provided by Spack
~~~~~~~~~~~~~~~~~~~~~~~~~

.. note:: The majority of users will not need to use the modules provided by Spack. The standard set of mdules available to users should cover most common use cases on ARCHER2.

The Spack package manager provides many more modules (particularly for libraries and 
dependencies) than are visible by default to users. If you wish to see or use the
modules provided by Spack, then you must first load the ``spack`` module:

::

   module load spack

Once this module is loaded, the ``module avail`` command will list the additional
modules that have been installed using Spack.

Care must be taken when using modules provided by Spack as they behave differently
from standard Linux modules.

The `Spack <http://spack.readthedocs.io>`__ package management tool is used
to manage much of the software and libraries installed on ARCHER2. Spack allows
us to automatically resolve dependencies and have multiple versions of tested
software installed simultaneously without them interfering with each other.

To achieve this, Spack makes use of RPATH to hardcode the paths of dependencies
into libraries. This means that when you load a module for a particular library
you do not need to load any further modules for dependencies of that library.

For example, the *boost* toolkit depends on the MPI, zlib and bzip2 libraries:

::

    boost@1.64.0
        ^bzip2@1.0.6
        ^mpich@2.14
        ^zlib@1.2.10

Spack arranges things so that if you load the boost module:

::

    module load boost-1.64.0-gcc-6.2.0-pftxg46

then you do not also need to load the bzip2, mpt and zlib modules.

This, however, can lead to behaviour that is unexpected for modules. For example,
on ARCHER2 there are two versions of zlib available: 1.2.8 and 1.2.10. You may
imagine that you can use boost with zlib 1.2.8 with the following commands:

::

    module load zlib-1.2.8-gcc-6.2.0-epathtp
    module load boost-1.64.0-gcc-6.2.0-pftxg46

**but this will not work**. boost will **still** use zlib 1.2.10 as the path
to this is hrdcoded into boost itself via RPATH. If you wish to use the 
older version of zlib then you must load it and then compile boost yourself.

If you wish to see what versions of libraries are hardcoded into a particular
Spack module then you must use Spack commands, e.g.

::

    [auser@archer2-login0 ~]$ module load spack
    [auser@archer2-login0 ~]$ module avail boost

    ------------ /lustre/sw/spack/share/spack/modules/linux-centos7-x86_64 ------------
    boost-1.63.0-intel-17.0.2-fl25xqn boost-1.64.0-gcc-6.2.0-pftxg46


    [auser@archer2-login0 ~]$ spack find -dl boost
    ==> 2 installed packages.
    -- linux-centos7-x86_64 / gcc@6.2.0 -----------------------------
    pftxg46    boost@1.64.0
    545wezu        ^bzip2@1.0.6
    kskvysh        ^mpich@2.14
    4og3my2        ^zlib@1.2.10


    -- linux-centos7-x86_64 / intel@17.0.2 --------------------------
    fl25xqn    boost@1.63.0
    nq2yt4x        ^bzip2@1.0.6
    jbjvxs7        ^zlib@1.2.10

This shows their are two boost modules installed (one for the Intel compilers
and one for the GCC compilers), they both depend on zlib 1.0.6 and bzip2 1.2.10
and the GCC version also depends on MPI 2.14 (HPE MPT 2.14). The paths for these
dependencies are hardocoded into the boost RPATH.


Available Compiler Suites
-------------------------

.. note::

   As ARCHER2 uses dynamic linking by default you will generally also need
   to load any modules you used to compile your code in your job submission
   script when you run your code.

Intel Compiler Suite
~~~~~~~~~~~~~~~~~~~~

The Intel compiler suite is accessed by loading the ``intel-compilers-*`` module. For example:

::

    module load intel-compilers-17

Once you have loaded the module, the compilers are available as:

* ``ifort`` - Fortran
* ``icc`` - C
* ``icpc`` - C++

C++ with Intel Compilers
^^^^^^^^^^^^^^^^^^^^^^^^

Intel compilers rely on GCC C++ headers and libraries to support most recent C++
features. If you are using Intel compilers to compile C++ on ARCHER2 you should 
also load the gcc/6.2.0 module to have access to the correct C++ files:

::
    module load gcc/6.2.0

.. note::

   You will also need to load this module in your job submission scripts
   when running code compiled in this way.

GCC Compiler Suite
~~~~~~~~~~~~~~~~~~

The GCC compiler suite is accessed by loading the ``gcc`` module. For example:

::

    module load gcc

Once you have loaded the module, the compilers are available as:

* ``gfortran`` - Fortran
* ``gcc`` - C
* ``g++`` - C++

Compiling MPI codes
-------------------

There are two MPI libraries currently available on ARCHER2:

* HPE Message Passing Toolkit (MPT)
* Intel MPI

The compilation and run commands are different depending on which of these
libraries you choose. Most of the applications we have compiled on ARCHER2
have made use of the HPE MPT library and we only use Intel MPI if HPE MPT
cannot be used for some reason. If you can use either library it is
worthwhile running a few tests to discover if either provides a performance
advantage for your application.

The following sections discuss each of the MPI library options in turn.

You should also consult the chapter on running jobs through the batch system
for examples of how to run jobs compiled against the different MPI libraries.

.. note::

   By default, all compilers produce dynamic executables on
   ARCHER2. This means that you must load the same modules at runtime (usually
   in your job submission script) as you have loaded at compile time.

Using HPE MPT
~~~~~~~~~~~~~

To compile MPI code with HPE MPT, using any compiler, you must first load the "mpt" module.

::

   module load mpt

This makes the compiler wrapper scripts ``mpicc``, ``mpicxx`` and ``mpif90`` available
to you.

What you do next depends on which compiler (Intel or GCC) you wish to use to
compile your code.

.. note::

   We recommend that you use the Intel compiler wherever possible to 
   compile MPI applications as this is the method officially supported and
   tested by HPE.

.. note::

   You can always check which compiler the MPI compiler wrapper scripts
   are using with, for example, ``mpicc -v`` or ``mpif90 -v``.

Using Intel Compilers and HPE MPT
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Once you have loaded the MPT module you should next load the appropriate 
``intel-compilers`` module (e.g. ``intel-compilers-17``):

::

    module load intel-compilers-17

Remember, if you are compiling C++ code, then you will also need to load the ``gcc/6.2.0`` module
for the C++ 11 headers to be available.

Compilers are then available as

* ``mpif90`` - Fortran with MPI
* ``mpicc`` - C with MPI
* ``mpicxx`` - C++ with MPI


.. note::

   mpicc uses gcc by default:

   When compiling C applications you must also specify that 
   ``mpicc`` should use the ``icc`` compiler with, for example,
   ``mpicc -cc=icc``. (This is not required for Fortran as the ``mpif90``
   compiler automatically uses ``ifort``.)  If in doubt use ``mpicc -cc=icc -v`` to see
   which compiler is actually being called.

   Alternatively, you can set the environment variable ``MPICC_CC=icc`` to 
   ensure the correct base compiler is used:

   ::

      export MPICC_CC=icc

.. note::

   mpicxx uses g++ by default:

   When compiling C++ applications you must also specify that 

   ``mpicxx`` should use the ``icpc`` compiler with, for example,
   ``mpicxx -cxx=icpc``. (This is not required for Fortran as the ``mpif90``
   compiler automatically uses ``ifort``.)  If in doubt use ``mpicxx -cxx=icpc -v`` to see
   which compiler is actually being called.

   Alternatively, you can set the environment variable ``MPICXX_CXX=icpc`` to 
   ensure the correct base compiler is used:

   ::

      export MPICXX_CXX=icpc

Using GCC Compilers and HPE MPT
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Once you have loaded the MPT module you should next load the 
``gcc`` module:

::

    module load gcc

Compilers are then available as

* ``mpif90`` - Fortran with MPI
* ``mpicc`` - C with MPI
* ``mpicxx`` - C++ with MPI

.. note::

   HPE MPT does not support the syntax ``use mpi`` in Fortran 
   applications with the GCC compiler ``gfortran``. You should use the
   older ``include "mpif.h"`` syntax when using GCC compilers with 
   ``mpif90``. If you cannot change this, then use the Intel MPI library.

Using Intel MPI
~~~~~~~~~~~~~~~

To compile MPI code with Intel MPI, using any compiler, you must first load the
"intel-mpi-17" module:

::

   module load intel-mpi-17

This makes the compiler wrapper scripts available to you. The name of the  wrapper
script depends on the compiler suite you are using. In summary:

+----------+----------+--------+
| Language | Intel    | GCC    |
+==========+==========+========+
| Fortran  | mpiifort | mpif90 |
+----------+----------+--------+
| C++      | mpiicpc  | mpicxx |
+----------+----------+--------+
| C        | mpiicc   | mpicc  |
+----------+----------+--------+

Further details on using the different compiler suites with Intel MPI are available
in the following sections.

Using Intel Compilers and Intel MPI
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Once you have loaded the ``intel-mpi-17`` module you should next load the appropriate 
``intel-compilers`` module (e.g. ``intel-compilers-17``):

::

    module load intel-compilers-17

Remember, if you are compiling C++ code, then you will also need to load the ``gcc/6.2.0`` module
for the C++ 11 headers to be available.

MPI compilers are then available as

* ``mpiifort`` - Fortran with MPI
* ``mpiicc`` - C with MPI
* ``mpiicpc`` - C++ with MPI

.. note:: Intel compilers with Intel MPI use non-standard compiler wrapper script names. If you use the standard names you will end up using the GCC compilers.

Using GCC Compilers and Intel MPI
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Once you have loaded the ``intel-mpi-17`` module you should next load the ``gcc`` module.

::

    module load gcc 

MPI compilers are then available as

* ``mpif90`` - Fortran with MPI
* ``mpicc`` - C with MPI
* ``mpicxx`` - C++ with MPI


Compiler Information and Options
--------------------------------

The manual pages for the different compiler suites are available:

GCC
    Fortran ``man gfortran`` ,
    C/C++ ``man gcc``
Intel
    Fortran ``man ifort`` ,
    C/C++ ``man icc``

Useful compiler options
~~~~~~~~~~~~~~~~~~~~~~~

Whilst difference codes will benefit from compiler optimisations in
different ways, for reasonable performance on ARCHER2, at least
initially, we suggest the following compiler options:

Intel
    ``-O2``
GNU
    ``-O2 -ftree-vectorize -funroll-loops -ffast-math``

When you have a application that you are happy is working correctly and has
reasonable performance you may wish to investigate some more aggressive
compiler optimisations. Below is a list of some further optimisations
that you can try on your application (Note: these optimisations may
result in incorrect output for programs that depend on an exact
implementation of IEEE or ISO rules/specifications for math functions):

Intel
    ``-fast``
GNU
    ``-Ofast -funroll-loops``

Vectorisation, which is one of the important compiler optimisations for
ARCHER2, is enabled by default as follows:

Intel
    At ``-O2`` and above
GNU
    At ``-O3`` and above or when using ``-ftree-vectorize``

To promote integer and real variables from four to eight byte precision
for FORTRAN codes the following compiler flags can be used:

Intel
    ``-real-size 64 -integer-size 64 -xAVX``
    (Sometimes the Intel compiler incorrectly generates AVX2
    instructions if the ``-real-size 64`` or ``-r8`` options are set.
    Using the ``-xAVX`` option prevents this.)
GNU
    ``-freal-4-real-8 -finteger-4-integer-8``

Using static linking/libraries
-------------------------------

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
libraries (i.e. all of the object code of referenced libraries are contained in the
executable file).  This has the advantage
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
