Quickstart for Developers
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

