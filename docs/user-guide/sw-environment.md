# Software environment

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

   - `module restore` - Restore the default module setup (i.e. as if you had logged
     out and back in again)
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

!!! important
    You should not use the `module purge` command on ARCHER2 as this will
    cause issues for the HPE Cray programming environment. If you wish to 
    reset your modules, you should use the `module restore` command
    instead.

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
  1) craype-x86-rome               6) cce/16.0.1             11) PrgEnv-cray/8.4.0
  2) libfabric/1.12.1.2.2.0.0      7) craype/2.7.23          12) bolt/0.8
  3) craype-network-ofi            8) cray-dsmml/0.2.2       13) epcc-setup-env
  4) perftools-base/23.09.0        9) cray-mpich/8.1.27      14) load-epcc-module
  5) xpmem/0.2.119-1.3_0_gnoinfo  10) cray-libsci/23.09.1.1

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

-------------- /mnt/lustre/a2fs-work4/work/y07/shared/archer2-lmod/utils/compiler/crayclang/10.0 --------------
   darshan/3.3.1

----------------------- /mnt/lustre/a2fs-work4/work/y07/shared/archer2-lmod/python/core -----------------------
   globus-cli/3.32.0 (D)    netcdf4/1.6.4      (D)    pytorch/1.13.1-gpu        tensorflow/2.9.3
   globus-cli/3.35.2        pytorch-gpu/1.13.1        pytorch/2.2.0      (D)    tensorflow/2.12.0 (D)
   matplotlib/3.7.2         pytorch/1.10.2            scons/4.8.1               tensorflow/2.13.0
   netcdf4/1.5.7            pytorch/1.13.0a0          seaborn/0.12.2

------------------------ /mnt/lustre/a2fs-work4/work/y07/shared/archer2-lmod/libs/core ------------------------
   aocl/3.1     (D)    gmp/6.3.0            mesa/23.3.3         parmetis/4.0.3        slepc/3.14.1
   aocl/4.0            gsl/2.8              metis/5.1.0         petsc/3.14.2          slepc/3.18.3       (D)
   boost/1.72.0        hypre/2.18.0         mkl/2023.0.0        petsc/3.18.5   (D)    superlu-dist/6.4.0
   boost/1.81.0 (D)    hypre/2.25.0  (D)    mumps/5.3.5         scotch/6.1.0          superlu-dist/8.1.2 (D)
   eigen/3.4.0         libxml2/2.9.7        mumps/5.5.1  (D)    scotch/7.0.3   (D)    superlu/5.2.2

------------------------ /mnt/lustre/a2fs-work4/work/y07/shared/archer2-lmod/apps/core ------------------------
   castep/23.11                    gromacs/2024.2                    onetep/6.1.43.0-GCC-MKL
   castep/24.1              (D)    lammps-gpu/2Aug2024               onetep/7.3.86-CCE-LibSci
   castep/25.11                    lammps-python/15Dec2023           onetep/7.3.86-GCC-LibSci
   code_saturne/8.0.3-cce15        lammps/2Aug2024-GPU               onetep/7.3.86-GCC-MKL
   code_saturne/8.0.3-gcc11 (D)    lammps/13Feb2024                  openfoam/com/v2106
   cp2k/cp2k-8.2.0                 lammps/15Dec2023                  openfoam/com/v2212        (D)
   cp2k/cp2k-9.1.0                 lammps/17Feb2023           (D)    openfoam/org/v9.20210903
   cp2k/cp2k-2022.2                lammps/29Aug2024                  openfoam/org/v10.20230119 (D)
   cp2k/cp2k-2023.1.xsmm           namd-gpu/3.0                      py-chemshell/23.0.3
   cp2k/cp2k-2023.1                namd-nosmp/2.14                   quantum_espresso/6.8
   cp2k/cp2k-2023.2                namd-nosmp/3.0             (D)    quantum_espresso/7.1      (D)
   cp2k/cp2k-2024.1                namd/2.14-nosmp                   quantum_espresso/7.3.1
   cp2k/cp2k-2024.2                namd/2.14                  (D)    tcl-chemshell/3.7.1
   cp2k/cp2k-2024.3         (D)    namd/3.0-gpu                      vasp/5/5.4.4.pl2-vtst
   elk/elk-7.2.42                  namd/3.0-nosmp                    vasp/5/5.4.4.pl2
   fhiaims/221103.0                namd/3.0                          vasp/6/6.4.1-vtst
   fhiaims/240920.0         (D)    nektar/5.2.0                      vasp/6/6.4.1
   gromacs-gpu/2022.4              nektar/5.5.0               (D)    vasp/6/6.4.2-mkl19
   gromacs/2022.4+plumed           nwchem/7.0.2                      vasp/6/6.4.2
   gromacs/2022.4-GPU              nwchem/7.2.2               (D)    vasp/6/6.4.3              (D)
   gromacs/2022.4           (D)    onetep/6.1.43.0-CCE-LibSci (D)    vasp/6/6.5.0
   gromacs/2023.4                  onetep/6.1.43.0-GCC-LibSci

----------------------- /mnt/lustre/a2fs-work4/work/y07/shared/archer2-lmod/utils/core ------------------------
   amd-uprof/4.0.341         extra-compilers/1.0        imagemagick/6.8.9           python-wrapper/0.1
   arm/forge/22.1.3          forge/22.1.3               imagemagick/7.1.0    (D)    reframe/4.2.1
   blender/4.2.2             forge/24.0          (D)    likwid/5.4.1-archer2        spindle/0.13
   bolt/0.8           (L)    forge/25.0.1               ncl/6.6.2                   tcl/8.6.13
   cdo/2.1.1                 gct/v6.2.20201212          nco/5.1.6                   tk/8.6.13
   cmake/3.18.4              gct/v6.2.20220524   (D)    ncview/2.1.11               usage-analysis/1.4
   cmake/3.21.3              genmaskcpu/1.0             osu-benchmarks/5.4.1        visidata/2.1
   cmake/3.29.4       (D)    gnuplot/5.4.2-simg         other-software/1.0          vmd/1.9.3-gcc11     (D)
   darshan-util/3.3.1        gnuplot/5.4.2       (D)    paraview/5.10.1             vmd/1.9.3-mpi-gcc11
   epcc-reframe/0.2          gnuplot/5.4.3              paraview/5.11.1             xthi/1.3
   epcc-setup-env     (L)    graphviz/10.0.1            paraview/5.13.0      (D)

------------------- /opt/cray/pe/lmod/modulefiles/mpi/crayclang/14.0/ofi/1.0/cray-mpich/8.0 -------------------
   cray-hdf5-parallel/1.12.2.1 (D)    cray-mpixlate/1.0.0.6        cray-parallel-netcdf/1.12.3.1 (D)
   cray-hdf5-parallel/1.12.2.7        cray-mpixlate/1.0.2   (D)    cray-parallel-netcdf/1.12.3.7

...output trimmed...

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

------------------------------- /opt/cray/pe/lmod/modulefiles/cpu/x86-rome/1.0 --------------------------------
   cray-fftw/3.3.10.3    cray-fftw/3.3.10.5 (D)

  Where:
   D:  Default Module

Module defaults are chosen based on Find First Rules due to Name/Version/Version modules found in the module tree.
See https://lmod.readthedocs.io/en/latest/060_locating.html for details.

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

  cray-mrnet: cray-mrnet/5.0.4, cray-mrnet/5.1.1

  cray-netcdf: cray-netcdf/4.9.0.1, cray-netcdf/4.9.0.7

  cray-netcdf-hdf5parallel: cray-netcdf-hdf5parallel/4.9.0.1, cray-netcdf-hdf5parallel/4.9.0.7

  cray-openshmemx: cray-openshmemx/11.5.7, cray-openshmemx/11.6.1

...output trimmed...

```

Now we know which versions are available, we can use
`module spider cray-netcdf-hdf5parallel/4.9.0.1` to find out how we can make
it available:

```
auser@ln03:~> module spider cray-netcdf-hdf5parallel/4.9.0.1

---------------------------------------------------------------------------------------------------------------
  cray-netcdf-hdf5parallel: cray-netcdf-hdf5parallel/4.9.0.1
---------------------------------------------------------------------------------------------------------------

    You will need to load all module(s) on any one of the lines below before the "cray-netcdf-hdf5parallel/4.9.0.1" module is available to load.

      aocc/3.2.0  cray-mpich/8.1.23  cray-hdf5-parallel/1.12.2.1
      cce/15.0.0  cray-mpich/8.1.23  cray-hdf5-parallel/1.12.2.1
      craype-network-none  cray-mpich/8.1.23  cray-hdf5-parallel/1.12.2.1
      craype-network-ofi  cray-mpich/8.1.23  cray-hdf5-parallel/1.12.2.1
      craype-network-ucx  cray-mpich/8.1.23  cray-hdf5-parallel/1.12.2.1
      gcc/10.3.0  cray-mpich/8.1.23  cray-hdf5-parallel/1.12.2.1
      gcc/11.2.0  cray-mpich/8.1.23  cray-hdf5-parallel/1.12.2.1
 
    Help:
      Release info:  /opt/cray/pe/netcdf-hdf5parallel/4.9.0.1/release_info

```

There is a lot of information here, but what the output is essentailly telling
us is that in order to have `cray-netcdf-hdf5parallel/4.9.0.1` available to 
load we need to have loaded a compiler (any version of CCE, GCC or AOCC), 
an MPI library (any version of cray-mpich) and `cray-hdf5-parallel` loaded.
As we always have a compiler and MPI library loaded, we can satisfy all of the
dependencies by loading `cray-hdf5-parallel`, and then we can use
`module avail cray-netcdf-hdf5parallel` again to show that the module is now
available to load:

```
auser@ln03:~> module load cray-hdf5-parallel
auser@ln03:~> module avail cray-netcdf-hdf5parallel

--- /opt/cray/pe/lmod/modulefiles/hdf5-parallel/crayclang/14.0/ofi/1.0/cray-mpich/8.0/cray-hdf5-parallel/1.12.2 ---
   cray-netcdf-hdf5parallel/4.9.0.1

Module defaults are chosen based on Find First Rules due to Name/Version/Version modules found in the module tree.
See https://lmod.readthedocs.io/en/latest/060_locating.html for details.

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
auser@uan01:~> module load gromacs/2022.4 
```

will load GROMACS version 2022.4 into your environment,
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

## cgroup control of login resources

Note that it not possible for a single user to  monopolise the resources on
a login node as this is controlled by cgroups. This means that a user cannot slow 
down the response time for other users.
