Software environment
====================

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

The software environment on ARCHER2 is primarily controlled through
the ``module`` command. By loading and switching software modules you
control which software and versions are available to you.

.. note::

  A module is a self-contained description of a software package - it
  contains the settings required to run a software package and, usually,
  encodes required dependencies on other software packages.

By default, all users on ARCHER2 start with the default software
environment loaded.

Software modules on ARCHER2 are provided by both Cray (usually known
as the *Cray Development Environment, CDE*) and by EPCC, who provide 
the Service Provision and Computational Science and Engineering 
services.

In this section, we provide:

  - A brief overview of the ``module`` command
  - A brief description of how the ``module`` command manipulates your environment

Using the ``module`` command
----------------------------

.. seealso::

  We only cover basic usage of the ``module`` command here. For full documentation
  please see the `Linux manual page on modules <http://linux.die.net/man/1/module>`__

The ``module`` command takes a subcommand to indicate what operation
you wish to perform. Common subcommands are:

  - ``module list [name]`` - List modules currently loaded in your environment,
    optionally filtered by ``[name]``
  - ``module avail [name]`` - List modules available, optionally filtered by ``[name]``
  - ``module load name`` - Load the module called ``name`` into your environment
  - ``module remove name`` - Remove the module called ``name`` from your environment
  - ``module swap old new`` - Swap module ``new`` for module ``old`` in your environment
  - ``module help name`` - Show help information on module ``name``
  - ``module show name`` - List what module ``name`` actually does to your environment

These are described in more detail below.

Information on the available modules
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The ``module list`` command will give the names of the modules
and their versions you have presently loaded in your environment:

.. TODO: Update with actual command output from system

::

    [user@archer2-login0 ~]$ module list
    Currently Loaded Modulefiles:
    1) mpt/2.14                        3) intel-fc-16/16.0.3.210
    2) intel-cc-16/16.0.3.210          4) intel-compilers-16/16.0.3.210

Finding out which software modules are available on the system is performed using the
``module avail`` command. To list all software modules available, use:

::

    [user@archer2-login0 ~]$ module avail
    ...

This will list all the names and versions of the modules available on
the service. Not all of them may work in your account though due to,
for example, licencing restrictions. You will notice that for many
modules we have more than one version, each of which is identified by a
version number. One of these versions is the default. As the
service develops the default version will change and old versions of
software may be deleted.

You can list all the modules of a particular type by providing an
argument to the ``module avail`` command. For example, to list all
available versions of the GROMACS software, use:

.. TODO: Update with actual command output from system

::

    [user@archer2-login0 ~]$ module avail gromacs
 
    --------------------------------------- /lustre/sw/modulefiles ---------------------------------------
    intel-compilers-16/16.0.2.181 intel-compilers-16/16.0.3.210

If you want more info on any of the modules, you can use the
``module help`` command:

.. TODO: Update with actual command output from system

::

    [user@archer2-login0 ~]$ module help gromacs

    ----------- Module Specific Help for 'mpt/2.14' -------------------

    The HPE Message Passing Toolkit (MPT) is an optimized MPI
    implementation for HPE systems and clusters.  See the
    MPI(1) man page and the MPT User's Guide for more
    information.

The ``module show`` command reveals what operations the module actually
performs to change your environment when it is loaded. We provide a brief
overview of what the significance of these different settings mean below.
For example, for the default GROMACS module:

.. TODO: Update with actual command output from system

::

    [user@archer2-login0 ~]$ module show gromacs

    ----------- Module Specific Help for 'mpt/2.14' -------------------

    The HPE Message Passing Toolkit (MPT) is an optimized MPI
    implementation for HPE systems and clusters.  See the
    MPI(1) man page and the MPT User's Guide for more
    information.

Loading, removing and swapping modules
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To load a module to use the ``module load`` command. For example,
to load the default version of GROMACS into your environment, use:

::

    module load gromacs

Once you have done this, your environment will be setup to use the 
GROMACS software (*e.g.* you can now use the ``gmx`` command). This version
of the command will load the default version of GROMACS. If
you need a specific version of the software, you can add more information:

::

    module load gromacs/2019.4

will load GROMACS version 2019.4 into your environment, regardless of the
default.

If you want to remove software from your environment, ``module remove`` will
remove a loaded module:

::

    module remove gromacs

will unload what ever version of ``gromacs`` (even if it is not the default)
you might have loaded. 

There are many situations in which you might want to change the
presently loaded version to a different one, such as trying the latest
version which is not yet the default or using a legacy version to keep
compatibility with old data. This can be achieved most easily by using 
"module swap oldmodule newmodule". 

Suppose you have loaded version 2020.1 of ``gromacs``, the following
command will change to version 2019.4:

::

    module swap gromacs gromacs/2019.4

You did not need to specify the version of the loaded module in your
current environment as this can be inferred as it will be the only one
you have loaded.

.. note::

  The ``module swap`` command is most often used on ARCHER2 to switch 
  between different compiler environments, *e.g.* Cray compilers to 
  GNU compilers. The software development environment is described in
  more detail in the :doc:`dev-environment` chapter.

Capturing your environment for reuse
------------------------------------

.. TODO: How to capture your current module environment

Shell environment overview
--------------------------

.. TODO: Add description here

Brief description of the shell environment variables and what they do.
