Quickstart for developers
=========================

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

This guide aims to quickly enable developers to work on ARCHER2.

Modules on ARCHER2
------------------

Software on ARCHER2 is principally accessed through environment modules. These
load and unload the desired compilers, tools and libraries through the
``module`` command and its subcommands. Some will be loaded by default on login,
providing a default working environment; many more will be available for use but
initially unloaded, allowing you to set up the environment to suit your needs.

At any stage you can check which modules have been loaded by running::

  module list

Running the following command will display all environment modules available on
ARCHER2, whether loaded or unloaded::

  module avail

The search field for this command may be narrowed by providing the first few
characters of the module name being queried. For example, all available versions
and variants of FFTW may be found by running::

  module avail fftw

You will see that different versions are available for many modules. For
example, ``fftw/3.3.4.5`` and ``fftw/3.3.4.11`` are two available versions of
FFTW. Furthermore, a default version may be specified and will be used if no
version is provided by the user.

The ``module load`` and ``module add`` commands perform the same action, loading
a module for use. Following the above,

::

  module load fftw

would load the default version of FFTW, while

::

  module load fftw/3.3.4.5

would specifically load version 3.3.4.5. A loaded module may be unloaded through
the identical ``module unload``, ``module remove`` or ``module delete``
commands, e.g.

::

  module unload fftw

which would unload whichever version of FFTW is currently in the environment.
Rather than issuing separate unload and load commands, versions of a module may
be swapped as follows::

  module swap fftw/3.3.4.5 fftw/3.3.4.11

Other helpful commands are:

* ``module help <modulename>`` which provides a short description of the module
* ``module show <modulename>`` which displays the contents of the modulefile
* ``module purge`` which unloads all modules, returning you to a clean
  environment

Points to be aware of include:

* Some modules will conflict with others. A simple example would be the conflict
  arising when trying to load a different version of an already loaded module.
  When a conflict occurs, the loading process will fail and an error message
  will be displayed. Examination of the message and the modulefiles (via
  ``module show``) should reveal the cause of the conflict and how to resolve
  it.
* The order in which modules are loaded *can* matter. Consider two modules
  which set the same variable to a different value. The final value
  would be that set by the module which loaded last. If you suspect that two
  modules may be interfering with one another, you can examine their contents
  with ``module show``.

Programming environments
------------------------

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

On login to ARCHER2, the ``PrgEnv-cray`` module will be loaded, as will a `cce`
module. The latter makes available Cray's compilers from the Cray Compiling
Environment (CCE), while the former provides the correct wrappers and support to
use them. The GNU Compiler Collection (GCC) and the AMD Optimizing Compiler
Collection (AOCC) compilers are also available. To make use of any of the three
Programming Environments, simply swap to the correct ``PrgEnv`` module. The
default version of the corresponding compiler suite will also be loaded, but you
may swap to another version if you wish.

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
gcc/5.3.0``. At this point you may invoke the wrappers and they will correctly
use Cray's libraries and tools in conjunction with GCC.

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