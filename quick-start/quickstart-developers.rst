Quickstart for developers
=========================

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

This guide aims to quickly enable developers to work on ARCHER2. It assumes
that you are familiar with the material in :doc:`quickstart-users`.

Compiler wrappers
-----------------

When compiling code on ARCHER2, you should make use of the Cray compiler
wrappers. These ensure that the correct libraries and headers (for example, MPI
or Cray LibSci) will be used during the compilation and linking stages. These
wrappers should be accessed by providing the following compiler names:

+----------+--------------+
| Language | Wrapper name |
+==========+==============+
| C        | cc           |
+----------+--------------+
| C++      | CC           |
+----------+--------------+
| Fortran  | ftn          |
+----------+--------------+

This means that you should use the wrapper names whether on the command line, in
build scripts, or in configure options. It could be helpful to set some or all
of the following environment variables before running a build to ensure that the
build tool is aware of the wrappers::

  export CC=cc
  export CXX=CC
  export FC=ftn
  export F77=ftn
  export F90=ftn

``man`` pages are available for each wrapper. You can also see the full set of
compiler and linker options being used by passing the ``-craype-verbose`` option
to the wrapper when using it.

Programming environments
------------------------

On login to ARCHER2, the ``PrgEnv-cray`` collection will be loaded, as will a
``cce`` module. The latter makes available Cray's compilers from the Cray
Compiling Environment (CCE), while the former provides the correct wrappers and
support to use them. The GNU Compiler Collection (GCC) is also available.

To make use of any Programming Environment, restore the correct ``PrgEnv``
collection. After doing so the compiler wrappers (``cc``, ``CC`` and ``ftn``) will
correctly call the compilers from the new suite. The default version of the
corresponding compiler suite will also be loaded, but you may swap to another
available version if you wish.

The following table summarises the suites and associated programming environments.

+------------+--------+------------------------------------+
| Suite name | Module | Programming environment collection |
+============+========+====================================+
| CCE        |``cce`` | ``PrgEnv-cray``                    |
+------------+--------+------------------------------------+
| GCC        |``gcc`` | ``PrgEnv-gnu``                     |
+------------+--------+------------------------------------+
| AOCC       |``aocc``| ``PrgEnv-aocc``                    |
+------------+--------+------------------------------------+

As an example, after logging in you may wish to use GCC as your compiler suite.
Running ``module restore PrgEnv-gnu`` will replace the Cray environment
with the GNU environment. It will also unload the ``cce`` module
and load the default version of the ``gcc`` module; at the time of writing, this
is GCC 10.1.0. If you need to use a different version of GCC, for example 9.3.0,
you would follow up with ``module swap gcc gcc/9.3.0``. At this point you may 
invoke the compiler wrappers and they will correctly use Cray's libraries and 
tools in conjunction with GCC 9.3.0.

When choosing the programming environment, a big factor will likely be which
compilers you have previously used for your code's development. The Cray Fortran
compiler is similar to the compiler you may be familiar with from ARCHER, while
the Cray C and C++ compilers provided on ARCHER2 are new versions that are now
derived from Clang. The GCC suite provides gcc/g++ and gfortran. The AOCC suite
provides AMD Clang/Clang++ and AMD Flang.

.. note::

  Unlike ARCHER, the Intel compilers are not available on ARCHER2.



.. TODO: Possibly - uncomment the following section if CDT modules become available.

.. Changing the version of the development environment
.. ---------------------------------------------------

.. The programming environment on ARCHER2, consisting of the compilers and
.. libraries, are versioned together under the Cray Developer Toolkit (CDT).
.. Software comprising the CDT will be updated over time. If you wish, you may
.. choose to use a given version over the default by loading the appropriate
.. module, e.g. for CDT 18.12::

..  module load cdt/18.12

.. A given CDT module will load those versions of the following software that
.. together make it up:

.. * Cray ATP (Abnormal Termination Processing)
.. * Cray LibSci
.. * Cray MPT (Message Passing Toolkit, providing MPI)
.. * Cray PMI (Process Manager Interface Library)
.. * The Cray Programming Environment
.. * The current compiler (dependent on which ``PrgEnv`` is active)

Useful compiler options
-----------------------

The compiler options you use will depend on both the software you are building
and also on the current stage of development. The following flags should be a
good starting point for reasonable performance:

+--------------+-------------------------------------------------------------------+
| Compilers    | Optimisation flags                                                |
+==============+===================================================================+
| Cray C/C++   | ``-O2 -funroll-loops -ffast-math``                                |
+--------------+-------------------------------------------------------------------+
| Cray Fortran | Default options                                                   |
+--------------+-------------------------------------------------------------------+
| GCC          | ``-O2 -ftree-vectorize -funroll-loops -ffast-math``               |
+--------------+-------------------------------------------------------------------+

When you are happy with your code's performance you may wish to enable more
aggressive optimisations; in this case you could start using the following
flags. Please note, however, that these optimisations may lead to deviations
from IEEE/ISO specifications. If your code relies on strict adherence then these
flags may lead to it producing incorrect output.

+--------------+-------------------------------------------------------------------+
| Compilers    | Optimisation flags                                                |
+==============+===================================================================+
| Cray C/C++   | ``-Ofast -funroll-loops``                                         |
+--------------+-------------------------------------------------------------------+
| Cray Fortran | ``-O3 -hfp3``                                                     |
+--------------+-------------------------------------------------------------------+
| GCC          | ``-Ofast -funroll-loops``                                         |
+--------------+-------------------------------------------------------------------+

Vectorisation is enabled by the Cray Fortran compiler at ``-O1`` and above, by
Cray C and C++ at ``-O2`` and above or when using ``-ftree-vectorize``, and by
the GCC compilers at ``-O3`` and above or when using ``-ftree-vectorize``.

You may wish to promote default ``real`` and ``integer`` types in Fortran codes
from 4 to 8 bytes. In this case, the following flags may be used:

+--------------+-------------------------------------------------------------------+
| Compiler     | Fortran ``real`` and ``integer`` promotion flags                  |
+==============+===================================================================+
| Cray Fortran | ``-s real64 -s integer64``                                        |
+--------------+-------------------------------------------------------------------+
| gfortran     | ``-freal-4-real-8 -finteger-4-integer-8``                         |
+--------------+-------------------------------------------------------------------+

More documentation on the compilers is available through ``man``. The pages to
read are accessed as follow:

+-----------------+-----------------+-----------------+------------------+
| Compiler suite  | C               | C++             | Fortran          |
+=================+=================+=================+==================+
| Cray            | ``man craycc``  | ``man crayCC``  | ``man crayftn``  |
+-----------------+-----------------+-----------------+------------------+
| GNU             | ``man gcc``     | ``man g++``     | ``man gfortran`` |
+-----------------+-----------------+-----------------+------------------+

.. note::

  There are no ``man`` pages for the AOCC compilers at the moment.

Linking on ARCHER2
------------------

Executables on ARCHER2 link dynamically, and the Cray Programming Environment
does not currently support static linking. This is in contrast to ARCHER where
the default was to build statically.

The compiler wrapper scripts on ARCHER link runtime libraries in using the
``runpath`` by default. This means that the paths to the runtime libraries
are encoded into the executable so you do not need to load the compiler 
environment in your job submission scripts.

Using RPATHs to link
^^^^^^^^^^^^^^^^^^^^

The default behaviour of a dynamically linked executable will be to allow the
linker to provide the libraries it needs at runtime by searching the paths in
the ``LD_LIBRARY_PATH`` environment variable. This is flexible in that it allows
an executable to use newly installed library versions without rebuilding, but in
some cases you may prefer to bake the paths to specific libraries into the
executable, keeping them constant. While the libraries are still dynamically
loaded at run time, from the end user's point of view the resulting behaviour
will be similar to that of a statically compiled executable in that they will
not need to concern themselves with ensuring the linker will be able to find the
libraries.

This is achieved by providing RPATHs to the compiler as options. To set the
compiler wrappers to do this, you can set the following environment variable::

  export CRAY_ADD_RPATH=yes

You can also provide RPATHs directly to the compilers using the
``-Wl,-rpath=<path-to-directory>`` flag, where the provided path is to the
directory containing the libraries which are themselves typically specified with
flags of the type ``-l<library-name>``.

Debugging tools
---------------

The following debugging tools are available on ARCHER2:

* **gdb4hpc** is a command-line tool working similarly to `gdb
  <https://www.gnu.org/software/gdb/>`_ that allows users to debug parallel
  programs. It can launch parallel programs or attach to ones already running and
  allows the user to step through the execution to identify the causes of any
  unexpected behaviour. Available via ``module load gdb4hpc``.
* **valgrind4hpc** is a parallel memory debugging tool that aids in detection of
  memory leaks and errors in parallel applications. It aggregates like errors 
  across processes and threads to simplify debugging of parallel appliciations. 
  Available via ``module load valgrind4hpc``.
* **STAT**, the Stack Trace Analysis Tool, generates merged stack traces for 
  parallel applications. It also provides visualisation tools. Available via 
  ``module load cray-stat``.
* **ATP**, Abnormal Termiation Processing, offers scalable core file and
  backtrace analysis when parallel programs crash. Output can be viewed with
  STAT. Available via ``module load atp``.
.. * **CCDB**, the Cray Comparative Debugger, allows you to compare two versions
  of code side-by-side to analyse differences. Available via 
  ``module load cray-ccdb`` and used in conjunction with gdb4hpc.

.. TODO: Uncomment CCDB from the list above if/when it is functional.

To get started debugging on ARCHER2, you might like to use gdb4hpc. You should
first of all compile your code using the ``-g`` flag to enable debugging symbols.
Once compiled, load the gdb4hpc module and start it::

  module load gdb4hpc
  gdb4hpc

Once inside gdb4hpc, you can start your program's execution with the ``launch``
command::

  dbg all> launch $my_prog{128} ./prog

In this example, a job called ``my_prog`` will be launched to run the executable
file ``prog`` over 128 cores on a compute node. If you run ``squeue`` in another
terminal you will be able to see it running. Inside gdb4hpc you may then
``step`` through the code's execution, ``continue`` to breakpoints that you set
with ``break``, ``print`` the values of variables at these points, and perform a
``backtrace`` on the stack if the program crashes. Debugging jobs will end when
you exit gdb4hpc, or you can end them yourself by running, in this example,
``release $my_prog``.

For more information on debugging parallel codes, see the documentation
at :doc:`ARCHER2 User and Best Practice Guide - Debugging
<../user-guide/debug>`.

.. TODO: Add more detail on using debuggers

.. note::

  We will add more information on using the debugging tools once the ARCHER2 system is available.

Profiling tools
---------------

Profiling on ARCHER2 is provided through the Cray Performance Measurement and
Analysis Tools (CrayPAT). This has a number of different components:

* **CrayPAT** the full-featured program analysis tool set. CrayPAT consists of
  pat_build, the utility used to instrument programs, the CrayPat run time
  environment, which collects the specified performance data during program
  execution, and pat_report, the first-level data analysis tool, used to produce
  text reports or export data for more sophisticated analysis
* **CrayPAT-lite** a simplified and easy-to-use version of CrayPAT that provides
  basic performance analysis information automatically, with a minimum of user
  interaction.
* **Reveal** the next-generation integrated performance analysis and code 
  optimization tool, which enables the user to correlate performance data 
  captured during program execution directly to the original source, and 
  identify opportunities for further optimization.
* **Cray PAPI** components, which are support packages for those who want to 
  access performance counters.
* **Cray Apprentice2** the second-level data analysis tool, used to visualize, 
  manipulate, explore, and compare sets of program performance data in a GUI 
  environment.

The above tools are made available for use by firstly loading the
``perftools-base`` module followed by either ``perftools`` (for CrayPAT, Reveal
and Apprentice2) or one of the ``perftools-lite`` modules.

The simplest way to get started profiling your code is with CrayPAT-lite. For
example, to sample a run of a code you would load the ``perftools-base`` and
``perftools-lite`` modules, and then compile (you will receive a message that
the executable is being instrumented). Performing a batch run as usual with this
executable will produce a directory such as ``my_prog+74653-2s`` which can be
passed to ``pat_report`` to view the results. In this example, 

::

  pat_report -O calltree+src my_prog+74653-2s

will produce a report containing the call tree.
You can view available report keywords to be provided to the ``-O`` option by
running ``pat_report -O -h``. The available ``perftools-lite`` modules are:

* ``perftools-lite``, instrumenting a basic sampling experiment.
* ``perftools-lite-events``, instrumenting a tracing experiment.
* ``perftools-lite-gpu``, instrumenting OpenACC and OpenMP 4 use of GPUs.
* ``perftools-lite-hbm``, instrumenting for memory bandwidth usage.
* ``perftools-lite-loops``, instrumenting a loop work estimate experiment.

For more information on profiling parallel codes, see the documentation
at :doc:`ARCHER2 User and Best Practice Guide - Profiling
<../user-guide/profile>`.

.. TODO: Add more detail on using profilers

.. note::

  We will add more information on using the profiling tools once the ARCHER2 system is available.

Useful Links
------------

Links to other documentation you may find useful:

* :doc:`ARCHER2 User and Best Practice Guide <../user-guide/overview>` - Covers all aspects of use of the ARCHER2 service. This includes fundamentals (required by all users to use the system effectively), best practice for getting the most out of ARCHER2, and more advanced technical topics.
* `Cray Programming Environment User Guide <https://pubs.cray.com/bundle/XC_Series_Programming_Environment_User_Guide_1705_S-2529/page/Record_of_Revision.html>`__
* `Cray Performance Measurement and Analysis Tools User Guide <https://pubs.cray.com/bundle/Cray_Performance_Measurement_and_Analysis_Tools_User_Guide_644_S-2376/page/About_the_Cray_Performance_Measurement_and_Analysis_Tools_User_Guide.html>`__

.. TODO: Update the two Cray documentation links to Shasta whenever/if ever this becomes available.