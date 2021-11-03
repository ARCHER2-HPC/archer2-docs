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
    behaves like `module list`; when a module name is specified to `ml`,
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
auser@ln03:~> module list

Currently Loaded Modules:
  1) cce/11.0.4              6) perftools-base/21.02.0
  2) craype/2.7.6            7) xpmem/2.2.40-7.0.1.0_2.7__g1d7a24d.shasta
  3) craype-x86-rome         8) cray-mpich/8.1.4
  4) libfabric/1.11.0.4.71   9) cray-libsci/21.04.1.1
  5) craype-network-ofi     10) PrgEnv-cray/8.0.0

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

------------- /opt/cray/pe/lmod/modulefiles/mpi/crayclang/10.0/ofi/1.0/cray-mpich/8.0 -------------
   cray-hdf5-parallel/1.12.0.3 (D)    cray-parallel-netcdf/1.12.1.3 (D)
   cray-hdf5-parallel/1.12.0.7        cray-parallel-netcdf/1.12.1.7

------------------------- /opt/cray/pe/lmod/modulefiles/perftools/21.02.0 -------------------------
   perftools         perftools-lite-events    perftools-lite-hbm      perftools-preload
   perftools-lite    perftools-lite-gpu       perftools-lite-loops

------------------- /opt/cray/pe/lmod/modulefiles/comnet/crayclang/10.0/ofi/1.0 -------------------
   cray-mpich-abi/8.1.4 (D)    cray-mpich-abi/8.1.9    cray-mpich/8.1.4 (L,D)    cray-mpich/8.1.9

---------------------------- /opt/cray/pe/lmod/modulefiles/net/ofi/1.0 ----------------------------
   cray-openshmemx/11.2.0 (D)    cray-openshmemx/11.3.3

------------------------- /opt/cray/pe/lmod/modulefiles/cpu/x86-rome/1.0 --------------------------
   cray-fftw/3.3.8.9 (D)    cray-fftw/3.3.8.11

---------------------- /opt/cray/pe/lmod/modulefiles/compiler/crayclang/10.0 ----------------------
   cray-hdf5/1.12.0.3 (D)    cray-hdf5/1.12.0.7

---------------- /opt/cray/pe/lmod/modulefiles/mpi/aocc/2.2/ofi/1.0/cray-mpich/8.0 ----------------
   cray-hdf5-parallel/1.12.0.3    cray-parallel-netcdf/1.12.1.3

------------------------------ /usr/share/lmod/lmod/modulefiles/Core ------------------------------
   lmod    settarg

------------------------------- /opt/cray/pe/lmod/modulefiles/core --------------------------------
   PrgEnv-aocc/8.0.0 (D)      cray-ccdb/4.12.4               craype/2.7.6           (L,D)
   PrgEnv-aocc/8.1.0          cray-cti/2.13.6       (D)      craype/2.7.10
   PrgEnv-cray/8.0.0 (L,D)    cray-cti/2.15.5                craypkg-gen/1.3.14     (D)
   PrgEnv-cray/8.1.0          cray-dsmml/0.1.4      (D)      craypkg-gen/1.3.18
aturner@ln03:~> module avail

------------- /opt/cray/pe/lmod/modulefiles/mpi/crayclang/10.0/ofi/1.0/cray-mpich/8.0 -------------
   cray-hdf5-parallel/1.12.0.3 (D)    cray-parallel-netcdf/1.12.1.3 (D)
   cray-hdf5-parallel/1.12.0.7        cray-parallel-netcdf/1.12.1.7

------------------------- /opt/cray/pe/lmod/modulefiles/perftools/21.02.0 -------------------------
   perftools         perftools-lite-events    perftools-lite-hbm      perftools-preload
   perftools-lite    perftools-lite-gpu       perftools-lite-loops

------------------- /opt/cray/pe/lmod/modulefiles/comnet/crayclang/10.0/ofi/1.0 -------------------
   cray-mpich-abi/8.1.4 (D)    cray-mpich-abi/8.1.9    cray-mpich/8.1.4 (L,D)    cray-mpich/8.1.9

---------------------------- /opt/cray/pe/lmod/modulefiles/net/ofi/1.0 ----------------------------
   cray-openshmemx/11.2.0 (D)    cray-openshmemx/11.3.3

------------------------- /opt/cray/pe/lmod/modulefiles/cpu/x86-rome/1.0 --------------------------
   cray-fftw/3.3.8.9 (D)    cray-fftw/3.3.8.11

---------------------- /opt/cray/pe/lmod/modulefiles/compiler/crayclang/10.0 ----------------------
   cray-hdf5/1.12.0.3 (D)    cray-hdf5/1.12.0.7

---------------- /opt/cray/pe/lmod/modulefiles/mpi/aocc/2.2/ofi/1.0/cray-mpich/8.0 ----------------
   cray-hdf5-parallel/1.12.0.3    cray-parallel-netcdf/1.12.1.3

------------------------------ /usr/share/lmod/lmod/modulefiles/Core ------------------------------
   lmod    settarg

------------------------------- /opt/cray/pe/lmod/modulefiles/core --------------------------------
   PrgEnv-aocc/8.0.0 (D)      cray-ccdb/4.12.4               craype/2.7.6           (L,D)
   PrgEnv-aocc/8.1.0          cray-cti/2.13.6       (D)      craype/2.7.10
   PrgEnv-cray/8.0.0 (L,D)    cray-cti/2.15.5                craypkg-gen/1.3.14     (D)
   PrgEnv-cray/8.1.0          cray-dsmml/0.1.4      (D)      craypkg-gen/1.3.18
   PrgEnv-gnu/8.0.0  (D)      cray-dsmml/0.2.1               gcc/9.3.0
   PrgEnv-gnu/8.1.0           cray-jemalloc/5.1.0.4          gcc/10.2.0             (D)
   aocc/2.2.0.1      (D)      cray-libpals/1.0.17            gcc/10.3.0
   aocc/3.0.0                 cray-libsci/21.04.1.1 (L,D)    gcc/11.2.0
   atp/3.13.1        (D)      cray-libsci/21.08.1.2          gdb4hpc/4.12.5         (D)
   atp/3.14.5                 cray-pals/1.0.17               gdb4hpc/4.13.5
   cce/11.0.4        (L,D)    cray-pmi-lib/6.0.10   (D)      iobuf/2.0.10
   cce/12.0.3                 cray-pmi-lib/6.0.13            papi/6.0.0.6           (D)
   cpe-cuda/21.09             cray-pmi/6.0.10       (D)      papi/6.0.0.9
   cpe/21.04         (D)      cray-pmi/6.0.13                perftools-base/21.02.0 (L,D)
   cpe/21.09                  cray-python/3.8.5.0   (D)      perftools-base/21.09.0
   cray-R/4.0.3.0    (D)      cray-python/3.9.4.1            valgrind4hpc/2.11.1    (D)
   cray-R/4.1.1.0             cray-stat/4.10.1      (D)      valgrind4hpc/2.12.4
   cray-ccdb/4.11.1  (D)      cray-stat/4.11.5

---------------------- /opt/cray/pe/lmod/modulefiles/craype-targets/default -----------------------
   craype-accel-amd-gfx908    craype-hugepages256M    craype-network-none
   craype-accel-amd-gfx90a    craype-hugepages2G      craype-network-ofi  (L)
   craype-accel-host          craype-hugepages2M      craype-network-ucx
   craype-accel-nvidia70      craype-hugepages32M     craype-x86-milan
   craype-accel-nvidia80      craype-hugepages4M      craype-x86-rome     (L)
   craype-hugepages128M       craype-hugepages512M    craype-x86-trento
   craype-hugepages16M        craype-hugepages64M
   craype-hugepages1G         craype-hugepages8M

-------------------------------------- /opt/cray/modulefiles --------------------------------------
   cray-lustre-client/2.12.4.2_cray_63_g79cd827-7.0.1.0_8.1__g79cd827237.shasta
   cray-shasta-mlnx-firmware/1.0.8
   dvs/2.12_4.0.112-7.0.1.0_15.1__ga97f35d9
   libfabric/1.11.0.4.71                                                        (L)
   xpmem/2.2.40-7.0.1.0_2.7__g1d7a24d.shasta                                    (L)

---------------------------------------- /opt/modulefiles -----------------------------------------
   aocc/2.2.0.1    aocc/3.0.0    cray-R/4.0.3.0    gcc/8.1.0    gcc/9.3.0    gcc/10.2.0

  Where:
   L:  Module is loaded
   D:  Default Module

Use "module spider" to find all possible modules and extensions.
Use "module keyword key1 key2 ..." to search for all possible modules matching any of the "keys".

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
auser@ln03:~>  module avail cray-fftw

------------------------- /opt/cray/pe/lmod/modulefiles/cpu/x86-rome/1.0 --------------------------
   cray-fftw/3.3.8.9 (D)    cray-fftw/3.3.8.11

  Where:
   D:  Default Module

Use "module spider" to find all possible modules and extensions.
Use "module keyword key1 key2 ..." to search for all possible modules matching any of the "keys".


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

For example, the module `cray-netcdf-hdf5parallel` is installed on ARCHER2 but it
will not be found by `module avail`:

```
auser@ln03:~> module avail cray-netcdf-hdf5parallel
No module(s) or extension(s) found!
Use "module spider" to find all possible modules and extensions.
Use "module keyword key1 key2 ..." to search for all possible modules matching any of the "keys".
```

We can use `module spider` without any arguments to verify it exists and list
the versions available:

```
auser@ln03:~> module spider

-----------------------------------------------------------------------------------------------
The following is a list of the modules and extensions currently available:
-----------------------------------------------------------------------------------------------

...output trimmed...

  cray-mpich-abi: cray-mpich-abi/8.1.4, cray-mpich-abi/8.1.9

  cray-netcdf: cray-netcdf/4.7.4.3, cray-netcdf/4.7.4.7

  cray-netcdf-hdf5parallel: cray-netcdf-hdf5parallel/4.7.4.3, cray-netcdf-hdf5parallel/4.7.4.7

  cray-openshmemx: cray-openshmemx/11.2.0, cray-openshmemx/11.3.3

...output trimmed...

```

Now we know which versions are available, we can use
`module spider cray-netcdf-hdf5parallel/4.7.4.7` to find out how we can make
it available:

```
auser@ln03:~> module spider cray-netcdf-hdf5parallel/4.7.4.3

-----------------------------------------------------------------------------------------------
  cray-netcdf-hdf5parallel: cray-netcdf-hdf5parallel/4.7.4.3
-----------------------------------------------------------------------------------------------

    You will need to load all module(s) on any one of the lines below before the "cray-netcdf-hdf5parallel/4.7.4.3" module is available to load.

      aocc/2.2.0.1  cray-mpich/8.1.4  cray-hdf5-parallel/1.12.0.3
      aocc/2.2.0.1  cray-mpich/8.1.4  cray-hdf5-parallel/1.12.0.7
      aocc/2.2.0.1  cray-mpich/8.1.9  cray-hdf5-parallel/1.12.0.3
      aocc/2.2.0.1  cray-mpich/8.1.9  cray-hdf5-parallel/1.12.0.7
      aocc/3.0.0  cray-mpich/8.1.4  cray-hdf5-parallel/1.12.0.3
      aocc/3.0.0  cray-mpich/8.1.4  cray-hdf5-parallel/1.12.0.7
      aocc/3.0.0  cray-mpich/8.1.9  cray-hdf5-parallel/1.12.0.3
      aocc/3.0.0  cray-mpich/8.1.9  cray-hdf5-parallel/1.12.0.7
      cce/11.0.4  cray-mpich/8.1.4  cray-hdf5-parallel/1.12.0.3
      cce/11.0.4  cray-mpich/8.1.4  cray-hdf5-parallel/1.12.0.7
      cce/11.0.4  cray-mpich/8.1.9  cray-hdf5-parallel/1.12.0.3
      cce/11.0.4  cray-mpich/8.1.9  cray-hdf5-parallel/1.12.0.7
      cce/12.0.3  cray-mpich/8.1.4  cray-hdf5-parallel/1.12.0.3
      cce/12.0.3  cray-mpich/8.1.4  cray-hdf5-parallel/1.12.0.7
      cce/12.0.3  cray-mpich/8.1.9  cray-hdf5-parallel/1.12.0.3
      cce/12.0.3  cray-mpich/8.1.9  cray-hdf5-parallel/1.12.0.7
      craype-network-none  cray-mpich/8.1.4  cray-hdf5-parallel/1.12.0.3
      craype-network-none  cray-mpich/8.1.4  cray-hdf5-parallel/1.12.0.7
      craype-network-none  cray-mpich/8.1.9  cray-hdf5-parallel/1.12.0.3
      craype-network-none  cray-mpich/8.1.9  cray-hdf5-parallel/1.12.0.7
      craype-network-ofi  cray-mpich/8.1.4  cray-hdf5-parallel/1.12.0.3
      craype-network-ofi  cray-mpich/8.1.4  cray-hdf5-parallel/1.12.0.7
      craype-network-ofi  cray-mpich/8.1.9  cray-hdf5-parallel/1.12.0.3
      craype-network-ofi  cray-mpich/8.1.9  cray-hdf5-parallel/1.12.0.7
      craype-network-ucx  cray-mpich/8.1.4  cray-hdf5-parallel/1.12.0.3
      craype-network-ucx  cray-mpich/8.1.4  cray-hdf5-parallel/1.12.0.7
      craype-network-ucx  cray-mpich/8.1.9  cray-hdf5-parallel/1.12.0.3
      craype-network-ucx  cray-mpich/8.1.9  cray-hdf5-parallel/1.12.0.7
      gcc/10.2.0  cray-mpich/8.1.4  cray-hdf5-parallel/1.12.0.3
      gcc/10.2.0  cray-mpich/8.1.4  cray-hdf5-parallel/1.12.0.7
      gcc/10.2.0  cray-mpich/8.1.9  cray-hdf5-parallel/1.12.0.3
      gcc/10.2.0  cray-mpich/8.1.9  cray-hdf5-parallel/1.12.0.7
      gcc/10.3.0  cray-mpich/8.1.4  cray-hdf5-parallel/1.12.0.3
      gcc/10.3.0  cray-mpich/8.1.4  cray-hdf5-parallel/1.12.0.7
      gcc/10.3.0  cray-mpich/8.1.9  cray-hdf5-parallel/1.12.0.3
      gcc/10.3.0  cray-mpich/8.1.9  cray-hdf5-parallel/1.12.0.7
      gcc/11.2.0  cray-mpich/8.1.4  cray-hdf5-parallel/1.12.0.3
      gcc/11.2.0  cray-mpich/8.1.4  cray-hdf5-parallel/1.12.0.7
      gcc/11.2.0  cray-mpich/8.1.9  cray-hdf5-parallel/1.12.0.3
      gcc/11.2.0  cray-mpich/8.1.9  cray-hdf5-parallel/1.12.0.7
      gcc/9.3.0  cray-mpich/8.1.4  cray-hdf5-parallel/1.12.0.3
      gcc/9.3.0  cray-mpich/8.1.4  cray-hdf5-parallel/1.12.0.7
      gcc/9.3.0  cray-mpich/8.1.9  cray-hdf5-parallel/1.12.0.3
      gcc/9.3.0  cray-mpich/8.1.9  cray-hdf5-parallel/1.12.0.7
 
    Help:
      Release info:  /opt/cray/pe/netcdf-hdf5parallel/4.7.4.7/release_info

```

There is a lot of information here, but what the output is essentailly telling
us is that in order to have `cray-netcdf-hdf5parallel/4.7.4.3` available to 
load we need to have loaded a compiler (any version of CCE, GCC or AOCC), 
an MPI library (any version of cray-mpich) and `cray-hdf5-parallel` loaded.
As we always have a compiler and MPI library loaded, we can satisfy all of the
dependencies by loading `cray-hdf5-parallel`, and then we can use
`module avail cray-netcdf-hdf5parallel` again to show that the module is now
available to load:

```
auser@ln03:~> module load cray-hdf5-parallel
auser@ln03:~> module avail cray-netcdf-hdf5parallel

----- /opt/cray/pe/lmod/modulefiles/hdf5-parallel/crayclang/10.0/ofi/1.0/cray-mpich/8.0/cray-hdf5-parallel/1.12.0 ------
   cray-netcdf-hdf5parallel/4.7.4.3 (D)    cray-netcdf-hdf5parallel/4.7.4.7

  Where:
   D:  Default Module

Use "module spider" to find all possible modules and extensions.
Use "module keyword key1 key2 ..." to search for all possible modules matching any of the "keys".

```

#### `module help`

If you want more info on any of the modules, you can use the `module
help` command:

```
auser@ln03:~> module help gromacs


```

#### `module show`

The `module show` command reveals what operations the module actually
performs to change your environment when it is loaded. For example, for
the default FFTW module:

```
auser@ln03:~> module show gromacs

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
the default version of GROMACS into your environment, use:

```
auser@ln03:~> module load gromacs
```

Once you have done this, your environment will be setup to use GROMACS.
The above command will load the default version of GROMACS. If you need
a specific version of the software, you can add more information:

```
auser@uan01:~> module load gromacs/2021.2
```

will load GROMACS version 2021.2 into your environment,
regardless of the default.

#### `module remove`

If you want to remove software from your environment, `module remove`
will remove a loaded module:

```
auser@uan01:~> module remove gromacs
```

will unload what ever version of `gromacs` you might have loaded (even
if it is not the default).

#### `module swap`

There are many situations in which you might want to change the
presently loaded version to a different one, such as trying the latest
version which is not yet the default or using a legacy version to keep
compatibility with old data. This can be achieved most easily by using
`module swap oldmodule newmodule`.

For example, to swap from the default CCE (cray) compiler environment
to the GCC (gnu) compiler environment, you would use:

```
auser@ln03:~> module swap PrgEnv-cray PrgEnv-gnu
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
As with any software, the *bash* shell has loaded a set of environment
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
