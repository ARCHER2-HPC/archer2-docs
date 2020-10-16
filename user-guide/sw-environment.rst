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

Software modules on ARCHER2 are provided by both HPE Cray (usually known
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
  - ``module savelist`` - List module collections available (usually used for accessing different programming environments)
  - ``module restore name`` - Restore the module collection called ``name`` (usually used for setting up a programming environment)
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

    auser@uan01:~> module list
Currently Loaded Modulefiles:
 1) cpe-aocc                          7) cray-dsmml/0.1.2(default)                           
 2) aocc/2.1.0.3(default)             8) perftools-base/20.09.0(default)                     
 3) craype/2.7.0(default)             9) xpmem/2.2.35-7.0.1.0_1.3__gd50fabf.shasta(default)  
 4) craype-x86-rome                  10) cray-mpich/8.0.15(default)                          
 5) libfabric/1.11.0.0.233(default)  11) cray-libsci/20.08.1.2(default)                      
 6) craype-network-ofi  

Finding out which software modules are available on the system is performed using the
``module avail`` command. To list all software modules available, use:

.. code-block:: console

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

This will list all the names and versions of the modules available on
the service. Not all of them may work in your account though due to,
for example, licencing restrictions. You will notice that for many
modules we have more than one version, each of which is identified by a
version number. One of these versions is the default. As the
service develops the default version will change and old versions of
software may be deleted.

You can list all the modules of a particular type by providing an
argument to the ``module avail`` command. For example, to list all
available versions of the HPE Cray FFTW library, use:

.. code-block:: console

  auser@uan01:~> module avail cray-fftw
 
  ---------------------------------------- /opt/cray/pe/modulefiles -----------------------------------------
  cray-fftw/3.3.8.7(default) 

If you want more info on any of the modules, you can use the
``module help`` command:

.. code-block:: console

  auser@uan01:~> module help cray-fftw

  -------------------------------------------------------------------
  Module Specific Help for /opt/cray/pe/modulefiles/cray-fftw/3.3.8.7:


  ===================================================================
  FFTW 3.3.8.7
  ============
    Release Date:
    -------------
      June 2020


    Purpose:
    --------
      This Cray FFTW 3.3.8.7 release is supported on Cray Shasta Systems. 
      FFTW is supported on the host CPU but not on the accelerator of Cray systems.

      The Cray FFTW 3.3.8.7 release provides the following:
        - Optimizations for AMD Rome CPUs.
      See the Product and OS Dependencies section for details
    
  [...]

The ``module show`` command reveals what operations the module actually
performs to change your environment when it is loaded. We provide a brief
overview of what the significance of these different settings mean below.
For example, for the default FFTW module:

.. code-block:: console

  auser@uan01:~> module show cray-fftw
  -------------------------------------------------------------------
  /opt/cray/pe/modulefiles/cray-fftw/3.3.8.7:

  conflict        cray-fftw
  conflict        fftw
  setenv          FFTW_VERSION 3.3.8.7
  setenv          CRAY_FFTW_VERSION 3.3.8.7
  setenv          CRAY_FFTW_PREFIX /opt/cray/pe/fftw/3.3.8.7/x86_rome
  setenv          FFTW_ROOT /opt/cray/pe/fftw/3.3.8.7/x86_rome
  setenv          FFTW_DIR /opt/cray/pe/fftw/3.3.8.7/x86_rome/lib
  setenv          FFTW_INC /opt/cray/pe/fftw/3.3.8.7/x86_rome/include
  prepend-path    PATH /opt/cray/pe/fftw/3.3.8.7/x86_rome/bin
  prepend-path    MANPATH /opt/cray/pe/fftw/3.3.8.7/share/man
  prepend-path    CRAY_LD_LIBRARY_PATH /opt/cray/pe/fftw/3.3.8.7/x86_rome/lib
  prepend-path    PE_PKGCONFIG_PRODUCTS PE_FFTW
  setenv          PE_FFTW_TARGET_x86_skylake x86_skylake
  setenv          PE_FFTW_TARGET_x86_rome x86_rome
  setenv          PE_FFTW_TARGET_x86_cascadelake x86_cascadelake
  setenv          PE_FFTW_TARGET_x86_64 x86_64
  setenv          PE_FFTW_TARGET_share share
  setenv          PE_FFTW_TARGET_sandybridge sandybridge
  setenv          PE_FFTW_TARGET_mic_knl mic_knl
  setenv          PE_FFTW_TARGET_ivybridge ivybridge
  setenv          PE_FFTW_TARGET_haswell haswell
  setenv          PE_FFTW_TARGET_broadwell broadwell
  setenv          PE_FFTW_VOLATILE_PKGCONFIG_PATH /opt/cray/pe/fftw/3.3.8.7/@PE_FFTW_TARGET@/lib/pkgconfig
  setenv          PE_FFTW_PKGCONFIG_VARIABLES PE_FFTW_OMP_REQUIRES_@openmp@
  setenv          PE_FFTW_OMP_REQUIRES { }
  setenv          PE_FFTW_OMP_REQUIRES_openmp _mp
  setenv          PE_FFTW_PKGCONFIG_LIBS fftw3_mpi:libfftw3_threads:fftw3:fftw3f_mpi:libfftw3f_threads:fftw3f
  module-whatis   {FFTW 3.3.8.7 - Fastest Fourier Transform in the West}
    [...]

Loading, removing and swapping modules
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To load a module to use the ``module load`` command. For example,
to load the default version of HPE Cray FFTW into your environment, use:

.. code-block:: console

  auser@uan01:~> module load cray-fftw

Once you have done this, your environment will be setup to use the HPE Cray FFTW
library. The above command will load the default version of HPE Cray FFTW. If
you need a specific version of the software, you can add more information:

.. code-block:: console

  auser@uan01:~> module load cray-fftw/3.3.8.7

will load HPE Cray FFTW version 3.3.8.7 into your environment, regardless of the
default.

If you want to remove software from your environment, ``module remove`` will
remove a loaded module:

.. code-block:: console

  auser@uan01:~> module remove cray-fftw

will unload what ever version of ``cray-fftw`` (even if it is not the default)
you might have loaded. 

There are many situations in which you might want to change the
presently loaded version to a different one, such as trying the latest
version which is not yet the default or using a legacy version to keep
compatibility with old data. This can be achieved most easily by using 
"module swap oldmodule newmodule". 

Suppose you have loaded version 3.3.8.7 of ``cray-fftw``, the following
command will change to version 3.3.8.5:

.. code-block:: console

  auser@uan01:~> module swap cray-fftw cray-fftw/3.3.8.5

You did not need to specify the version of the loaded module in your
current environment as this can be inferred as it will be the only one
you have loaded.

Capturing your environment for reuse
------------------------------------

.. TODO Update section once Lmod is installed on ARCHER2

Sometimes it is useful to save the module environment that you are using to
compile a piece of code or execute a piece of software. This is known as a
module *collection* You can save a collection from your current environment 
by executing:

.. code-block:: console

  auser@uan01:~> module save [collection_name]

.. note::

  If you do not specify the environment name, it is called ``default``.

You can find the list of saved module environments by executing:

.. code-block:: console

  auser@uan01:~> module savelist
  Named collection list:
   1) default   2) PrgEnv-aocc   3) PrgEnv-cray   4) PrgEnv-gnu 

.. note::

  All users have three PrgEnv collections available. These are used
  to setup the different programming environments for compiling 
  software.

You can load a saved module environment by executing:

.. code-block:: console

  auser@uan01:~> module restore PrgEnv-gnu
  Unloading cray-fftw/3.3.8.7
  Unloading cray-libsci/20.08.1.2
  Unloading cray-mpich/8.0.15
  Unloading xpmem/2.2.35-7.0.1.0_1.3__gd50fabf.shasta

  Unloading perftools-base/20.09.0
    WARNING: Did not unuse /opt/cray/pe/perftools/20.09.0/modulefiles

  Unloading cray-dsmml/0.1.2
  Unloading craype-network-ofi
  Unloading libfabric/1.11.0.0.233
  Unloading craype-x86-rome

  Unloading craype/2.7.0
    WARNING: Did not unuse /opt/cray/pe/craype/2.7.0/modulefiles

  Unloading aocc/2.1.0.3
  Unloading cpe-aocc
  Loading cpe-gnu
  Loading gcc/10.1.0
  Loading craype/2.7.0
  Loading craype-x86-rome
  Loading libfabric/1.11.0.0.233
  Loading craype-network-ofi
  Loading cray-dsmml/0.1.2
  Loading perftools-base/20.09.0
  Loading xpmem/2.2.35-7.0.1.0_1.3__gd50fabf.shasta
  Loading cray-mpich/8.0.15
  Loading cray-libsci/20.08.1.2

To list the saved environment modules, you can execute:

.. code-block:: console

  auser@uan01:~> module saveshow
  -------------------------------------------------------------------
  /home/t01/t01/atuser/.module/default:

  module use --append /opt/cray/pe/perftools/20.09.0/modulefiles
  module use --append /opt/cray/pe/craype/2.7.0/modulefiles
  module use --append /usr/local/Modules/modulefiles
  module use --append /opt/cray/pe/cpe-prgenv/7.0.0
  module use --append /opt/modulefiles
  module use --append /opt/cray/modulefiles
  module use --append /opt/cray/pe/modulefiles
  module use --append /opt/cray/pe/craype-targets/default/modulefiles
  module load cpe-aocc
  module load aocc
  module load craype
  module load craype-x86-rome
  module load --notuasked libfabric
  module load craype-network-ofi
  module load cray-dsmml
  module load perftools-base
  module load xpmem
  module load cray-mpich
  module load cray-libsci
  module load cray-fftw

To delete a module environment, you can execute:

.. code-block:: console

  auser@uan01:~> module saverm <environment_name>

Shell environment overview
--------------------------

When you log in to ARCHER2, you are using the *bash* shell by default. As any
other software, the *bash* shell has loaded a set of environment variables
that can be listed by executing ``printenv`` or ``export``.

The environment variables listed before are useful to define the behaviour of
the software you run. For instance, ``OMP_NUM_THREADS`` define the number of
threads.

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


