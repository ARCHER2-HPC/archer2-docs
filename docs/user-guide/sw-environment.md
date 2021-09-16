# Software environment: full system

!!! important
    This section covers the software environment on the full
    ARCHER2 system. For docmentation on the software environment on the
    initial, 4-cabinet ARCHER2 system, please see [Software environment: 4-cabinet system](sw-environment-4cab.md).

The software environment on ARCHER2 is managed using the 
[Lmod](https://lmod.readthedocs.io/) software. Selecting which software
is available in your environment is primarily controlled through the
`module` command. By loading and switching software modules you control
which software and versions are available to you.

!!! information
    A module is a self-contained description of a software package -- it
    contains the settings required to run a software package and, usually,
    encodes required dependencies on other software packages.

By default, all users on ARCHER2 start with the default software
environment loaded.

Software modules on ARCHER2 are provided by both HPE (usually known
as the *HPE Cray Programming Environment, CPE*) and by EPCC, who provide the
Service Provision, and Computational Science and Engineering services.

In this section, we provide:

   - A brief overview of the `module` command
   - A brief description of how the `module` command manipulates your
     environment

## Using the `module` command

We only cover basic usage of the Lmod `module` command here. For full
documentation please see the [Lmod documentation](https://lmod.readthedocs.io/en/latest/010_user.html)

The `module` command takes a subcommand to indicate what operation you
wish to perform. Common subcommands are:

   - `module list [name]` - List modules currently loaded in your
     environment, optionally filtered by `[name]`
   - `module avail [name]` - List modules available, optionally
     filtered by `[name]`
   - `module spider [name][/version]` - Search available modules (including hidden
      modules) and provide information on modules
   - `module load name` - Load the module called `name` into your
     environment
   - `module remove name` - Remove the module called `name` from your
     environment
   - `module swap old new` - Swap module `new` for module `old` in your
     environment
   - `module help name` - Show help information on module `name`
   - `module show name` - List what module `name` actually does to your
     environment

These are described in more detail below.

!!! tip
    Lmod allows you to use the `ml` shortcut command. Without any arguments, `ml`
    behaves like `module list`; when a module name is speciied to `ml`,
    `ml` behaves like `module load`.

!!! note
    You will often have to include `module` commands in any job submission
    scripts to setup the software to use in your jobs. Generally, if you
    load modules in interactive sessions, these loaded modules do not
    carry over into any job submission scripts.

### Information on the available modules

The key commands for getting information on modules are covered in more
detail below. They are:

 - `module list`
 - `module avail`
 - `module spider`
 - `module help`
 - `module show`

#### `module list`

The `module list` command will give the names of the modules and their
versions you have presently loaded in your environment:

```
auser@uan01:~> module list
Currently Loaded Modulefiles:
1) cpe-aocc                          7) cray-dsmml/0.1.2(default)
2) aocc/2.1.0.3(default)             8) perftools-base/20.09.0(default)
3) craype/2.7.0(default)             9) xpmem/2.2.35-7.0.1.0_1.3__gd50fabf.shasta(default)
4) craype-x86-rome                  10) cray-mpich/8.0.15(default)
5) libfabric/1.11.0.0.233(default)  11) cray-libsci/20.08.1.2(default)
6) craype-network-ofi
```

All users start with a default set of modules loaded corresponding to:

 - The HPE Cray Compiling Environment (CCE): includes the HPE Cray clang and Fortran compilers
 - HPE Cray MPICH: The HPE Cray MPI library
 - HPE Cray LibSci: The HPE Cray numerical libraries (including BLAS/LAPACK and ScaLAPACK)


#### `module avail`

Finding out which software modules are currently available to load on the system is
performed using the `module avail` command. To list all software modules
currently available to load, use:

```
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
```

This will list all the names and versions of the modules that you can currently
load. Note that other modules may be defined but not available to you as they depend
on modules you do not have loaded. Lmod only shows modules that you can currently
load, not all those that are defined. You can search for modules
that are not currently visble to you using the `module spider` command - we 
cover this in more detail below.

Note also, that not all modules may work in your account though due to, for
example, licencing restrictions. You will notice that for many modules
we have more than one version, each of which is identified by a version
number. One of these versions is the default. As the service develops
the default version will change and old versions of software may be
deleted.

You can list all the modules of a particular type by providing an
argument to the `module avail` command. For example, to list all
available versions of the HPE Cray FFTW library, use:

```
auser@uan01:~> module avail cray-fftw

---------------------------------------- /opt/cray/pe/modulefiles -----------------------------------------
cray-fftw/3.3.8.7(default) 
```

#### `module spider`

The `module spider` command is used to find out which modules are defined on
the system. Unlike `module avail`, this includes modules that are not currently
able to be loaded due to the fact you have not yet loaded dependencies to make
them directly available.

`module spider` takes 3 forms:

 - `module spider` without any arguments lists all modules defined on the system
 - `module spider <module>` shows information on which versions of `<module>` are
   defined on the system
 - `module spider <module>/<version>` shows information on the specific version of 
   the module defined on the system, including dependencies that must be loaded 
   before this module can be loaded (if any)

If you cannot find a module that you expect to be on the system using `module avail`
then you can use `module spider` to find out which dependencies you need to load
to make the module available.

<!-- TODO add example of using module spider to inspect netcdf-hdf5-parallel -->

#### `module help`

If you want more info on any of the modules, you can use the `module
help` command:

```
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
```

#### `module show`

The `module show` command reveals what operations the module actually
performs to change your environment when it is loaded. For example, for
the default FFTW module:

```
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
```

### Loading, removing and swapping modules

To change your environment and make different software available you use the
following commands which we cover in more detail below.

 - `module load`
 - `module remove`
 - `module swap`

#### `module load`

To load a module to use the `module load` command. For example, to load
the default version of HPE Cray FFTW into your environment, use:

```
auser@uan01:~> module load cray-fftw
```

Once you have done this, your environment will be setup to use the HPE
Cray FFTW library. The above command will load the default version of
HPE Cray FFTW. If you need a specific version of the software, you can
add more information:

```
auser@uan01:~> module load cray-fftw/3.3.8.7
```

will load HPE Cray FFTW version 3.3.8.7 into your environment,
regardless of the default.

#### `module remove`

If you want to remove software from your environment, `module remove`
will remove a loaded module:

```
auser@uan01:~> module remove cray-fftw
```

will unload what ever version of `cray-fftw` (even if it is not the
default) you might have loaded.

#### `module swap`

There are many situations in which you might want to change the
presently loaded version to a different one, such as trying the latest
version which is not yet the default or using a legacy version to keep
compatibility with old data. This can be achieved most easily by using
`module swap oldmodule newmodule`.

Suppose you have loaded version 3.3.8.7 of `cray-fftw`, the following
command will change to version 3.3.8.5:

```
auser@uan01:~> module swap cray-fftw cray-fftw/3.3.8.5
```

You did not need to specify the version of the loaded module in your
current environment as this can be inferred as it will be the only one
you have loaded.

!!! tip
    `module swap` is most commonly used on ARCHER2 to switch between
    different compiler environments, for example, switching from 
    the HPE Cray Compiler Environment (CCE, PrgEnv-cray) to the
    Gnu compilers (GCC, PrgEnv-gnu). The available compiler environments
    are discussed in more detail in the [Application Development Environment](dev-environment.md)
    section.

## Shell environment overview

When you log in to ARCHER2, you are using the *bash* shell by default.
As any other software, the *bash* shell has loaded a set of environment
variables that can be listed by executing `printenv` or `export`.

The environment variables listed before are useful to define the
behaviour of the software you run. For instance, `OMP_NUM_THREADS`
define the number of threads.

To define an environment variable, you need to execute:

```
export OMP_NUM_THREADS=4
```

Please note there are no blanks between the variable name, the
assignation symbol, and the value. If the value is a string, enclose the
string in double quotation marks.

You can show the value of a specific environment variable if you print
it:

```
echo $OMP_NUM_THREADS
```

Do not forget the dollar symbol. To remove an environment variable, just
execute:

```
unset OMP_NUM_THREADS
```

Note that the dollar symbol is not included when you use the `unset` command.
