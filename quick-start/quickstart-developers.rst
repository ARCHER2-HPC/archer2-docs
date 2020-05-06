Quickstart for developers
=========================

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

This guide aims to quickly enable developers to work on ARCHER2. It assumes
that you are familiar with the material in :doc:`quickstart-users`.

Compiler wrappers
-----------------

When compiling code on ARCHER2, it is recommended that you make use of the Cray
compiler wrappers. These ensure that the correct libraries and headers (for
example, MPI or Cray LibSci) are included during all stages of compilation and
linking. These wrappers should be accessed by providing the following compiler
names, whether on the command line or in build scripts or configure options:

+----------+--------------+
| Language | Wrapper name |
+==========+==============+
| C        | cc           |
+----------+--------------+
| C++      | CC           |
+----------+--------------+
| Fortran  | ftn          |
+----------+--------------+

``man`` pages are available for each wrapper. You can see the full set of
compiler and linker options by passing the ``-craype-verbose`` option to the
wrapper.

Programming environments
------------------------

On login to ARCHER2, the ``PrgEnv-cray`` module will be loaded, as will a `cce`
module. The latter makes available Cray's compilers from the Cray Compiling
Environment (CCE), while the former provides the correct wrappers and support to
use them. The GNU Compiler Collection (GCC) and the AMD Optimizing Compiler
Collection (AOCC) compilers are also available. To make use of any of the three
Programming Environments, simply swap to the correct ``PrgEnv`` module. At this
point the compiler wrappers (``cc``, ``CC`` and ``ftn``) will correctly call the
compilers from the new suite. The default version of the corresponding compiler
suite will also be loaded, but you may swap to another version if you wish.

The following table summarises the suites and associated programming environments.

+------------+--------+--------------------------------+
| Suite name | Module | Programming environment module |
+============+========+================================+
| AOCC       |``aocc``| ``PrgEnv-amd``                 |
+------------+--------+--------------------------------+
| CCE        |``cce`` | ``PrgEnv-cray``                |
+------------+--------+--------------------------------+
| GCC        |``gcc`` | ``PrgEnv-gnu``                 |
+------------+--------+--------------------------------+

As an example, after logging in you may wish to use GCC as your compiler suite.
Running ``module swap PrgEnv-cray PrgEnv-gnu`` will unload the Cray environment
and replace it with the GNU environment. It will also unload the ``cce`` module
and load the default version of the ``gcc`` module. If you need to use a
different version, for example 5.3.0, you would follow up with ``module swap gcc
gcc/5.3.0``. At this point you may invoke the compiler wrappers and they will
correctly use Cray's libraries and tools in conjunction with GCC.

When choosing the programming environment, a big factor will likely be which
compilers you have previously used for your code's development. The Cray Fortran
compiler is similar to the compiler you may be familiar with from ARCHER, while
the Cray C and C++ compilers provided on ARCHER2 are new versions that are now
derived from Clang. The AMD C/C++ and Fortran compilers are respectively derived
from Clang and Flang. The GCC suite provides gcc and gfortran.

Please note that unlike ARCHER, the Intel compilers are not available on
ARCHER2.

Changing the version of the development environment
---------------------------------------------------

The programming environment on ARCHER2, consisting of the compilers and
libraries, are versioned together under the Cray Developer Toolkit (CDT).
Software comprising the CDT will be updated over time. If you wish, you may
choose to use a given version over the default by loading the appropriate
module, e.g. for CDT 18.12::

  module load cdt/18.12

A given CDT module will load those versions of the following software that
together make it up:

* Cray ATP (Abnormal Termination Processing)
* Cray LibSci
* Cray MPT (Message Passing Toolkit, providing MPI)
* Cray PMI (Process Manager Interface Library)
* The Cray Programming Environment
* The current compiler (dependent on which ``PrgEnv`` is active)

Useful compiler options
-----------------------

The compiler options you use will depend on both the software you are building
and also on the current stage of development. The following flags should be a
good starting point for reasonable performance:

+------------+-------------------------------------------------------------------+
| Compilers  | Optimisation flags                                                |
+============+===================================================================+
| Cray       | Default options                                                   |
+------------+-------------------------------------------------------------------+
| AMD        | **investigate**                                                   |
+------------+-------------------------------------------------------------------+
| GNU        | ``-O2 -ftree-vectorize -funroll-loops -ffast-math``               |
+------------+-------------------------------------------------------------------+

When you are happy with your code's performance you may wish to enable more
aggressive optimisations; in this case you could start using the following
flags. Please note, however, that these optimisations may lead to deviations
from IEEE/ISO specifications. If your code relies on strict adherence then these
flags may lead to it producing incorrect output.

+------------+-------------------------------------------------------------------+
| Compilers  | Optimisation flags                                                |
+============+===================================================================+
| Cray       | ``-O3 -hfp3``                                                     |
+------------+-------------------------------------------------------------------+
| AMD        | **investigate**                                                   |
+------------+-------------------------------------------------------------------+
| GNU        | ``-Ofast -funroll-loops``                                         |
+------------+-------------------------------------------------------------------+

Vectorisation is enabled by the Cray compilers at ``-O1`` and above, by the AMD
compilers at **find out** and above, and by the GNU compilers at ``-O3`` and
above or when using ``-ftree-vectorize``.

You may wish to promote default ``real`` and ``integer`` types in Fortran codes
from 4 to 8 bytes. In this case, the following flags may be used:

+------------+-------------------------------------------------------------------+
| Compiler   | Fortran ``real`` and ``integer`` promotion flags                  |
+============+===================================================================+
| Cray       | ``-O3 -hfp3``                                                     |
+------------+-------------------------------------------------------------------+
| AMD        | **investigate**                                                   |
+------------+-------------------------------------------------------------------+
| GNU        | ``-freal-4-real-8 -finteger-4-integer-8``                         |
+------------+-------------------------------------------------------------------+

Linking on ARCHER2
------------------

Executables on ARCHER2 will, by default, link dynamically. This is in contrast to
ARCHER where the default was to build statically.

Passing the ``-static`` or ``-dynamic`` flags to the wrappers will set that
behaviour. Alternatively, the behaviour of the compiler wrappers for your
current login shell can be changed by setting the ``CRAYPE_LINK_TYPE``
environment variable as follows::

  export CRAYPE_LINK_TYPE=static

to build static executables from now on, or

::

  export CRAYPE_LINK_TYPE=dynamic

to return to the default dynamic behaviour.

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

* **gdb4hpc** is a command-line debugging tool provided by Cray. It works similarly to
  [gdb](https://www.gnu.org/software/gdb/), but allows the user to debug multiple parallel processes
  without multiple windows. gdb4hpc can be used to investigate deadlocked code, segfaults, and other
  errors for C/C++ and Fortran code. Users can single-step code and focus on specific processes groups
  to help identify unexpected code behavior. (text from [ALCF](https://www.alcf.anl.gov/support-center/theta/gdb)).
* **valgrind4hpc** is a parallel memory debugging tool that aids in detection of memory leaks and
  errors in parallel applications. It aggregates like errors across processes and threads to simply
  debugging of parallel appliciations.
* **STAT** generate merged stack traces for parallel applications. Also has visualisation tools.
* **ATP** scalable core file and backtrace analysis when parallel programs crash.
* **CCDB** Cray Comparative Debugger. Compare two versions of code side-by-side to analyse differences.

.. TODO: Add more detail on using debuggers

.. note::

  We will add more information on using the debugging tools once the ARCHER2 system is available.

Profiling tools
---------------

Profiling on ARCHER2 is provide through the Cray Performance Measurement and Analysis Tools (CrayPat). This has
a number of different components:

* **CrayPAT** the full-featured program analysis tool set. CrayPat in turn consists of the following major components.
    * pat_build, the utility used to instrument programs
    * the CrayPat run time environment, which collects the specified performance data during program execution
    * pat_report, the first-level data analysis tool, used to produce text reports or export data for more sophisticated analysis
* **CrayPAT-lite** a simplified and easy-to-use version of CrayPat that provides basic performance analysis information automatically, with a minimum of user interaction.
* **Reveal** the next-generation integrated performance analysis and code optimization tool, which enables the user to correlate performance data captured during program execution directly to the original source, and identify opportunities for further optimization.
* **Cray PAPI** components, which are support packages for those who want to access performance counters
* **Cray Apprentice2** the second-level data analysis tool, used to visualize, manipulate, explore, and compare sets of program performance data in a GUI environment.

.. TODO: Add more detail on using debuggers

.. note::

  We will add more information on using the profiling tools once the ARCHER2 system is available.

Useful Links
------------

Links to other documentation you may find useful:

* :doc:`ARCHER2 User and Best Practice Guide <../user-guide/overview>` - Covers all aspects of use of the ARCHER2 service. This includes fundamentals (required by all users to use the system effectively), best practice for getting the most out of ARCHER2, and more advanced technical topics.
* `Cray Programming Environment User Guide <https://pubs.cray.com/content/S-2529/17.05/xctm-series-programming-environment-user-guide-1705-s-2529/introduction>`__
* `Cray Performance Measurement and Analysis Tools User Guide <https://pubs.cray.com/content/S-2376/7.0.0/cray-performance-measurement-and-analysis-tools-user-guide/about-the-cray-performance-measurement-and-analysis-tools-user-guide>`__