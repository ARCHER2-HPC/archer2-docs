Software environment
====================

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

The software environment on ARCHER2 is primarily controlled through
the ``module`` command. By loading and switching software modules you
control which software and versions are available to you.

.. note::

  A module is a self-contained description of a software package -- it
  contains the settings required to run a software package and, usually,
  encodes required dependencies on other software packages.

By default, all users on ARCHER2 start with the default software
environment loaded.

Software modules on ARCHER2 are provided by both Cray (usually known
as the *Cray Development Environment, CDE*) and by EPCC, who provide 
the Service Provision, and Computational Science and Engineering 
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

.. code-block:: console

    auser@login01-nmn:~> module list
    Currently Loaded Modulefiles:
     1) cce/10.0.0(default)                                 
     2) cray-libsci/20.03.1.4(default)                      
     3) cray-mpich/8.0.10(default)                          
     4) PrgEnv-cray/7.0.0(default)                          
     5) craype/2.6.4(default)                               
     6) craype-x86-rome                                     
     7) libfabric/1.10.0.0.249(default)                     
     8) craype-network-slingshot10                          
     9) cray-dsmml/0.1.0(default)                           
    10) perftools-base/20.05.0(default)                     
    11) xpmem/2.2.35-7.0.1.0_3.9__gfa8d091.shasta(default)

Finding out which software modules are available on the system is performed using the
``module avail`` command. To list all software modules available, use:

.. code-block:: console

    auser@login01-nmn:~> module avail
    ----------------------- /opt/cray/pe/perftools/20.05.0/modulefiles ------------------------
    perftools       perftools-lite-events  perftools-lite-hbm    perftools-nwpc     
    perftools-lite  perftools-lite-gpu     perftools-lite-loops  perftools-preload  
    
    -------------------------- /opt/cray/pe/craype/2.6.4/modulefiles --------------------------
    craype-hugepages1G  craype-hugepages8M   craype-hugepages128M  craype-network-slingshot10  
    craype-hugepages2G  craype-hugepages16M  craype-hugepages256M  craype-x86-rome             
    craype-hugepages2M  craype-hugepages32M  craype-hugepages512M  
    craype-hugepages4M  craype-hugepages64M  craype-network-none   
    
    ----------------------------- /usr/local/Modules/modulefiles ------------------------------
    dot  module-git  module-info  modules  null  use.own  
    
    -------------------------------- /opt/cray/pe/modulefiles ---------------------------------
    atp/3.5.4(default)                         cray-openshmemx/10.1.0(default)         
    cce/10.0.0(default)                        cray-parallel-netcdf/1.11.1.1(default)  
    cray-ccdb/4.5.4(default)                   cray-pmi-lib/6.0.5(default)             
    cray-cti/2.5.6(default)                    cray-pmi/6.0.5(default)                 
    cray-dsmml/0.1.0(default)                  cray-stat/4.4.5(default)                
    cray-fftw/3.3.8.4(default)                 craype-dl-plugin-py3/20.05.1            
    cray-fftw/3.3.8.5                          craype/2.6.4(default)                   
    cray-ga/5.7.0.3                            craypkg-gen/1.3.9(default)              
    cray-hdf5-parallel/1.10.5.2(default)       gdb4hpc/4.5.6(default)                  
    cray-hdf5/1.10.5.2(default)                iobuf/2.0.9(default)                    
    cray-libsci/20.03.1.4(default)             papi/5.7.0.3(default)                   
    cray-mpich-abi/8.0.10                      perftools-base/20.05.0(default)         
    cray-mpich/8.0.10(default)                 PrgEnv-cray/7.0.0(default)              
    cray-netcdf-hdf5parallel/4.6.3.2(default)  PrgEnv-gnu/7.0.0(default)               
    cray-netcdf/4.6.3.2(default)               valgrind4hpc/2.5.5(default)             

    ---------------------------------- /opt/cray/modulefiles ----------------------------------
    capsules/0.8.3(default)                                                                 
    chapel/1.20.1(default)                                                                  
    cray-lustre-client/2.12.0.5_cray_166_gf6711cf-7.0.1.0_2.24__gf6711cfb3.shasta(default)  
    cray-shasta-mlnx-firmware/1.0.5(default)                                                
    dvs/2.12_2.2.259-7.0.1.0_6.1__g6ee74127(default)                                        
    libfabric/1.10.0.0.249(default)                                                         
    spark/3.0.0(default)                                                                    
    xpmem/2.2.35-7.0.1.0_3.9__gfa8d091.shasta(default)                                      
    
    ------------------------------------ /opt/modulefiles -------------------------------------
    cray-python/3.8.2.0(default)  cray-R/3.6.3(default)  gcc/8.1.0  gcc/9.3.0(default)

This will list all the names and versions of the modules available on
the service. Not all of them may work in your account though due to,
for example, licencing restrictions. You will notice that for many
modules we have more than one version, each of which is identified by a
version number. One of these versions is the default. As the
service develops the default version will change and old versions of
software may be deleted.

You can list all the modules of a particular type by providing an
argument to the ``module avail`` command. For example, to list all
available versions of the FFTW library, use:

.. code-block:: console

    auser@login01-nmn:~> module avail cray-fftw
 
    --------------------------- /opt/cray/pe/modulefiles ---------------------------
    cray-fftw/3.3.8.4(default)  cray-fftw/3.3.8.5

If you want more info on any of the modules, you can use the
``module help`` command:

.. code-block:: console

    auser@login01-nmn:~> module help cray-fftw

    -------------------------------------------------------------------
    Module Specific Help for /opt/cray/pe/modulefiles/cray-fftw/3.3.8.4:
    
    
    ===================================================================
    FFTW 3.3.8.4
    ============
      Release Date:
      -------------
        November 2019
    
    
      Purpose:
      --------
        This Cray FFTW 3.3.8.4 release is supported on Cray Systems. 
        FFTW is supported on the host CPU but not on the accelerator of Cray systems.
    
        The Cray FFTW 3.3.8.4 release provides the following:
          - Support for AMD Rome CPUs.
        See the Product and OS Dependencies section for details.
    
    
    [...]

The ``module show`` command reveals what operations the module actually
performs to change your environment when it is loaded. We provide a brief
overview of what the significance of these different settings mean below.
For example, for the default FFTW module:

.. code-block:: console

    auser@login01-nmn:~> module show cray-fftw

    -------------------------------------------------------------------
    /opt/cray/pe/modulefiles/cray-fftw/3.3.8.4:

    conflict        cray-fftw
    conflict        fftw
    setenv          FFTW_VERSION 3.3.8.4
    setenv          CRAY_FFTW_VERSION 3.3.8.4
    setenv          CRAY_FFTW_PREFIX /opt/cray/pe/fftw/3.3.8.4/x86_rome
    setenv          FFTW_ROOT /opt/cray/pe/fftw/3.3.8.4/x86_rome
    setenv          FFTW_DIR /opt/cray/pe/fftw/3.3.8.4/x86_rome/lib
    setenv          FFTW_INC /opt/cray/pe/fftw/3.3.8.4/x86_rome/include
    prepend-path    PATH /opt/cray/pe/fftw/3.3.8.4/x86_rome/bin
    prepend-path    MANPATH /opt/cray/pe/fftw/3.3.8.4/share/man
    prepend-path    CRAY_LD_LIBRARY_PATH /opt/cray/pe/fftw/3.3.8.4/x86_rome/lib
    setenv          PE_FFTW_REQUIRED_PRODUCTS PE_MPICH
    prepend-path    PE_PKGCONFIG_PRODUCTS PE_FFTW
    setenv          PE_FFTW_TARGET_broadwell broadwell
    setenv          PE_FFTW_TARGET_haswell haswell
    setenv          PE_FFTW_TARGET_ivybridge ivybridge
    setenv          PE_FFTW_TARGET_mic_knl mic_knl
    setenv          PE_FFTW_TARGET_sandybridge sandybridge
    setenv          PE_FFTW_TARGET_share share
    setenv          PE_FFTW_TARGET_x86_64 x86_64
    [...]

Loading, removing and swapping modules
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To load a module to use the ``module load`` command. For example,
to load the default version of FFTW into your environment, use:

.. code-block:: console

    module load cray-fftw

Once you have done this, your environment will be setup to use the FFTW library.
This version of the command will load the default version of FFTW. If
you need a specific version of the software, you can add more information:

.. code-block:: console

    module load cray-fftw/3.3.8.5

will load FFTW version 3.3.8.5 into your environment, regardless of the
default.

If you want to remove software from your environment, ``module remove`` will
remove a loaded module:

.. code-block:: console

    module remove cray-fftw

will unload what ever version of ``cray-fftw`` (even if it is not the default)
you might have loaded. 

There are many situations in which you might want to change the
presently loaded version to a different one, such as trying the latest
version which is not yet the default or using a legacy version to keep
compatibility with old data. This can be achieved most easily by using 
"module swap oldmodule newmodule". 

Suppose you have loaded version 3.3.8.4 of ``cray-fftw``, the following
command will change to version 3.3.8.5:

.. code-block:: console

    module swap cray-fftw cray-fftw/3.3.8.5

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

.. TODO Update section once Lmod is installed on ARCHER2

Sometimes it is useful to save the module environment that you are using to
compile a piece of code or execute a piece of software. You can save the list of
loaded modules by executing:

.. code-block:: console

    module save [environment_name]

.. note::

    If you do not specify the environment name, it is called ``default``.

You can find the list of saved module enviroments by executing:

.. code-block:: console

    module savelist

You can load a saved module environment by executing:

.. code-block:: console

    module restore <environment_name>

To list the saved environment modules, you can execute:

.. code-block:: console

    module saveshow

To delete a module environment, you can execute:

.. code-block:: console

    module saverm <environment_name>

Shell environment overview
--------------------------

When you log in to ARCHER2, you are using the *bash* shell by default. As any
other software, the *bash* shell has loaded a set of environment variables
that can be listed by executing ``printenv`` or ``export``.

The environment variables listed before are useful to define the behaviour of
the software you run. For instance, ``OMP_NUM_THREADS`` define the number of
threads. Another example is the environment variable ``CRAYPE_LINK_TYPE``, which
defines the linking type (static or dynamic).

To define an environment variable, you need to execute:

.. code-block:: console

    export OMP_NUM_THREADS=4

Please note there are no blanks between the variable name, the assignation
symbol, and the value. If the value is a string, enclose the string in double
quotation marks.

You can show the value of a specific environment variable if you print it:

.. code-block:: console

    echo $OMP_NUM_THREADS

Do not forget the dollar symbol.
To remove an environment variable, just execute:

.. code-block:: console

    unset OMP_NUM_THREADS


