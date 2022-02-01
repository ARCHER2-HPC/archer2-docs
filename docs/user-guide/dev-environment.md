# Application development environment

## What's available

ARCHER2 runs the HPE Cray Linux Environment (a version of SUSE Linux),
and provides a development environment which includes:

  - Software modules via a standard module framework
  - Three different compiler environments (AMD, Cray, and GNU)
  - MPI, OpenMP, and SHMEM
  - Scientific and numerical libraries
  - Parallel Python and R
  - Parallel debugging and profiling
  - Singularity containers

Access to particular software, and particular versions, is managed by an 
Lmod module framework. Most software is available by loading modules,
including the different compiler environments

You can see what compiler environments are available with:

```
auser@uan01:~> module avail PrgEnv

------------------------------------------ /opt/cray/pe/lmod/modulefiles/core ------------------------------------------
   PrgEnv-aocc/8.0.0 (D)    PrgEnv-cray/8.0.0 (L,D)    PrgEnv-gnu/8.0.0 (D)
   PrgEnv-aocc/8.1.0        PrgEnv-cray/8.1.0          PrgEnv-gnu/8.1.0

  Where:
   L:  Module is loaded
   D:  Default Module

Use "module spider" to find all possible modules and extensions.
Use "module keyword key1 key2 ..." to search for all possible modules matching any of the "keys".

```

Other software modules can be searched using the `module spider` command:

``` 
auser@uan01:~> module spider

--------------------------------------------------------------------------------------------------------------------
The following is a list of the modules and extensions currently available:
--------------------------------------------------------------------------------------------------------------------
  PrgEnv-aocc: PrgEnv-aocc/8.0.0, PrgEnv-aocc/8.1.0

  PrgEnv-cray: PrgEnv-cray/8.0.0, PrgEnv-cray/8.1.0

  PrgEnv-gnu: PrgEnv-gnu/8.0.0, PrgEnv-gnu/8.1.0

  aocc: aocc/2.2.0.1, aocc/3.0.0

  atp: atp/3.13.1, atp/3.14.5

  cce: cce/11.0.4, cce/12.0.3

  cpe: cpe/21.04, cpe/21.09

...output trimmed...


  perftools-lite-hbm: perftools-lite-hbm

  perftools-lite-loops: perftools-lite-loops

  perftools-preload: perftools-preload

  settarg: settarg

  valgrind4hpc: valgrind4hpc/2.11.1, valgrind4hpc/2.12.4

  xpmem: xpmem/2.2.40-7.0.1.0_2.7__g1d7a24d.shasta

--------------------------------------------------------------------------------------------------------------------

To learn more about a package execute:

   $ module spider Foo

where "Foo" is the name of a module.

To find detailed information about a particular package you
must specify the version if there is more than one version:

   $ module spider Foo/11.1

--------------------------------------------------------------------------------------------------------------------

```

A full discussion of the module system is available in
[the Software environment section](sw-environment.md).

A consistent set of modules is loaded on login to the machine (currently
`PrgEnv-cray`, see below). Developing applications then means selecting
and loading the appropriate set of modules before starting work.

This section is aimed at code developers and will concentrate on the
compilation environment, building libraries and executables,
specifically parallel executables. Other topics such as [Python](python.md) and
[Containers](containers.md) are covered in more detail in separate sections of the
documentation.

!!! tip
    If you want to get back to the login module state without having to logout
    and back in again, you can just use:
    ```
    module restore
    ```
    This is also handy for build scripts to ensure you are starting from a known
    state.

## Compiler environments

There are three different compiler environments available on ARCHER2:

- AMD Compiler Collection (AOCC)
- GNU Compiler Collection (GCC)
- HPE Cray Compiler Collection (CCE) (current default compiler environment)

The current compiler suite is selected via the
`PrgEnv` module , while the specific compiler versions are
determined by the relevant compiler module. A summary is:

| Suite name | Compiler Environment Module | Compiler Version Module |
| ---------- | --------------------------- | ----------------------- |
| CCE        | `PrgEnv-cray`               | `cce`                   |
| GCC        | `PrgEnv-gnu`                | `gcc`                   |
| AOCC       | `PrgEnv-aocc`               | `aocc`                  |

For example, at login, the default set of modules are:

``` 
auser@ln03:~> module list

Currently Loaded Modules:
  1) cce/11.0.4              6) perftools-base/21.02.0
  2) craype/2.7.6            7) xpmem/2.2.40-7.0.1.0_2.7__g1d7a24d.shasta
  3) craype-x86-rome         8) cray-mpich/8.1.4
  4) libfabric/1.11.0.4.71   9) cray-libsci/21.04.1.1
  5) craype-network-ofi     10) PrgEnv-cray/8.0.0

```

from which we see the default compiler environment is Cray (indicated
by `PrgEnv-cray` (at 10 in the list above) and the default compiler module
is `cce/11.0.4` (at 1 in the list above). The compiler environment
will give access to a consistent set of compiler, MPI library via
`cray-mpich` (at 8), and other libraries e.g., `cray-libsci` (at 9 in
the list above) infrastructure.

### Switching between compiler environments

Switching between different compiler environments is achieved using the
`module swap` command. For example, to switch from the default HPE Cray
(CCE) compiler environment to the GCC environment, you would use:

```
auser@ln03:~> module swap PrgEnv-cray PrgEnv-gnu

Due to MODULEPATH changes, the following have been reloaded:
  1) cray-mpich/8.1.4

```

If you then use the `module list` command, you will see that your environment
has been changed to the GCC environment:

```
auser@ln03:~> module list

Currently Loaded Modules:
  1) gcc/10.2.0              6) perftools-base/21.02.0
  2) craype/2.7.6            7) xpmem/2.2.40-7.0.1.0_2.7__g1d7a24d.shasta
  3) craype-x86-rome         8) cray-mpich/8.1.4
  4) libfabric/1.11.0.4.71   9) cray-libsci/21.04.1.1
  5) craype-network-ofi     10) PrgEnv-gnu/8.0.0


```

### Switching between compiler versions

Within a given compiler environment, it is possible to swap to a
different compiler version by swapping the relevant compiler module.
To switch to the GNU compiler environment from the default HPE Cray compiler
environment and than swap the version of GCC from the 11.2.0 default to 
the older 10.2.0 version, you would use

```
auser@ln03:~> module swap PrgEnv-cray PrgEnv-gnu

Due to MODULEPATH changes, the following have been reloaded:
  1) cray-mpich/8.1.4

auser@ln03:~> module swap gcc gcc/11.2.0

The following have been reloaded with a version change:
  1) gcc/10.2.0 => gcc/11.2.0

```

The first swap command moves to the GNU compiler environment and the second
swap command moves to the older version of GCC. As before, `module list`
will show that your environment has been changed:

```
auser@ln03:~> module list

Currently Loaded Modules:
  1) craype/2.7.6            5) perftools-base/21.02.0                      9) gcc/11.2.0
  2) craype-x86-rome         6) xpmem/2.2.40-7.0.1.0_2.7__g1d7a24d.shasta  10) cray-mpich/8.1.4
  3) libfabric/1.11.0.4.71   7) cray-libsci/21.04.1.1
  4) craype-network-ofi      8) PrgEnv-gnu/8.0.0

```

### Compiler wrapper scripts: `cc`, `CC`, `ftn`

To ensure consistent behaviour, compilation of C, C++, and Fortran
source code should then take place using the appropriate compiler
wrapper: `cc`, `CC`, and `ftn`, respectively. The wrapper will
automatically call the relevant underlying compiler and add the
appropriate include directories and library locations to the invocation.
This typically eliminates the need to specify this additional
information explicitly in the configuration stage. To see the details of
the exact compiler invocation use the `-craype-verbose` flag to the
compiler wrapper.

The default link time behaviour is also related to the current
programming environment. See the section below on [Linking and
libraries](#linking-and-libraries).

Users should not, in general, invoke specific compilers at compile/link
stages. In particular, `gcc`, which may default to `/usr/bin/gcc`,
should not be used. The compiler wrappers `cc`, `CC`, and `ftn` should
be used (with the underlying compiler type and version set by the
module system). Other common MPI compiler wrappers
e.g., `mpicc`, should also be replaced by the relevant wrapper, e.g. `cc`
(commands such as `mpicc` are not available on ARCHER2).

!!! important
    Always use the compiler wrappers `cc`, `CC`, and/or `ftn` and not a
    specific compiler invocation. This will ensure consistent compile/link
    time behaviour.


!!! tip
    If you are using a build system such as Make or CMake then you 
    will need to replace all occurrences of `mpicc` with `cc`,
    `mpicxx`/`mpic++` with `CC` and `mpif90` with `ftn`.

### Compiler man pages and help

Further information on both the compiler wrappers, and the individual
compilers themselves are available via the command line, and via
standard `man` pages. The `man` page for the compiler wrappers is common
to all programming environments, while the `man` page for individual
compilers depends on the currently loaded programming environment. The
following table summarises options for obtaining information on the
compiler and compile options:

| Compiler suite | C            | C++           | Fortran        |
| -------------- | ------------ | ------------- | -------------- |
| Cray           | `man clang`  | `man clang++` | `man crayftn`  |
| GNU            | `man gcc`    | `man g++`     | `man gfortran` |
| Wrappers       | `man cc`     | `man CC`      | `man ftn`      |

!!! tip
    You can also pass the `--help` option to any of the compilers or
    wrappers to get a summary of how to use them. The Cray Fortran
    compiler uses `ftn --craype-help` to access the help options.

!!! tip
    There are no `man` pages for the AOCC compilers at the moment.

!!! tip
    Cray C/C++ is based on Clang and therefore
    supports similar options to clang/gcc. `clang --help` will produce a full summary
    of options with Cray-specific options marked "Cray". The `clang` man
    page on ARCHER2 concentrates on these Cray extensions to the `clang` front end and
    does not provide an exhaustive description of all `clang` options.
    Cray Fortran **is not** based on Flang and so takes different options
    from flang/gfortran.

### Which compiler environment?

If you are unsure which compiler you should choose, we suggest the
starting point should be the GNU compiler collection (GCC,
`PrgEnv-gnu`); this is perhaps the most commonly used by code
developers, particularly in the open source software domain. A portable,
standard-conforming code should (in principle) compile in any of the
three compiler environments.

For users requiring specific compiler features, such as co-array
Fortran, the recommended starting point would be Cray. The following
sections provide further details of the different compiler
environments.

!!! warning
    Intel compilers are not currently available on ARCHER2.


### GNU compiler collection (GCC)

The commonly used open source GNU compiler collection is available and
provides C/C++ and Fortran compilers.

Switch the the GCC compiler environment from the default CCE (cray)
compiler environment via:

```
auser@ln03:~> module swap PrgEnv-cray PrgEnv-gnu

Due to MODULEPATH changes, the following have been reloaded:
  1) cray-mpich/8.1.4

```

!!! bug
    The `gcc/8.1.0` module is available on ARCHER2 but cannot be used as the
    supporting scientific and system libraries are not available. You should
    **not** use this version of GCC.

!!! warning
    If you want to use GCC version 10 or greater to compile Fortran code,
    with the old MPI interfaces (i.e. `use mpi` or `INCLUDE 'mpif.h'`) you
    **must** add the `-fallow-argument-mismatch` option (or equivalent) when compiling
    otherwise you will see compile errors associated with MPI functions.
    The reason for this is that past versions of `gfortran` have allowed
    mismatched arguments to external procedures (e.g., where an explicit
    interface is not available). This is often the case for MPI routines
    using the old MPI interfaces where arrays of different types are passed
    to, for example, `MPI_Send()`. This will now generate an error as not
    standard conforming. The `-fallow-argument-mismatch` option is used
    to reduce the error to a warning. The same effect may be achieved via
    `-std=legacy`.

    If you use the Fortran 2008 MPI interface (i.e. `use mpi_f08`) then you
    should not need to add this option.

    Fortran language MPI bindings are described in more detail at
    in [the MPI Standard documentation](https://www.mpi-forum.org/docs/mpi-3.1/mpi31-report/node408.htm).

#### Useful Gnu Fortran options

| Option | Comment |  
| ------ | ------- |
| `-std=<standard>` |	Default is gnu |
| `-fallow-argument-mismatch` | Allow mismatched procedure arguments. This argument is required for compiling MPI Fortran code with GCC version 10 or greater if you are using the older MPI interfaces (see warning above) |
| `-fbounds-check` | Use runtime checking of array indices |
| `-fopenmp` | Compile OpenMP (default is no OpenMP) |
| `-v` | Display verbose output from compiler stages |

!!! tip
    The `standard` in `-std` may be one of `f95` `f2003`, `f2008` or
    `f2018`. The default option `-std=gnu` is the latest Fortran standard
    plus gnu extensions.

!!! warning
    Past versions of `gfortran` have allowed mismatched arguments to
    external procedures (e.g., where an explicit interface is not
    available). This is often the case for MPI routines where arrays of
    different types are passed to `MPI_Send()` and so on. This will now
    generate an error as not standard conforming. Use
    `-fallow-argument-mismatch` to reduce the error to a warning. The same
    effect may be achieved via `-std=legacy`.

#### Reference material

  - C/C++ documentation  
    <https://gcc.gnu.org/onlinedocs/gcc-9.3.0/gcc/>

  - Fortran documentation  
    <https://gcc.gnu.org/onlinedocs/gcc-9.3.0/gfortran/>

### Cray Compiling Environment (CCE)

The Cray Compiling Environment (CCE) is the default compiler at the point
of login. CCE supports C/C++ (along with unified parallel C UPC), and
Fortran (including co-array Fortran). Support for OpenMP parallelism is
available for both C/C++ and Fortran (currently OpenMP 4.5, with a
number of exceptions).

The Cray C/C++ compiler is based on a clang front end, and so compiler
options are similar to those for gcc/clang. However, the Fortran
compiler remains based around Cray-specific options. Be sure to separate
C/C++ compiler options and Fortran compiler options (typically `CFLAGS`
and `FFLAGS`) if compiling mixed C/Fortran applications.

As CCE is the default compiler environment on ARCHER2, you do not usually
need to issue any commands to enable CCE.

!!! note
    The CCE Clang compiler uses a GCC 8 toolchain so only C++ standard
    library features available in GCC 8 will be available in CCE Clang.
    You can add the compile option `--gcc-toolchain=/opt/gcc/10.2.0/snos`
    to use a more recent version of the C++ standard library if you
    wish.

#### Useful CCE C/C++ options

When using the compiler wrappers `cc` or `CC`, some of the following
options may be
useful:

Language, warning, Debugging options:

| Option | Comment |  
| ------ | ------- |
| `-std=<standard>` | Default is `-std=gnu11` (`gnu++14` for C++) \[1\] |
| `--gcc-toolchain=/opt/gcc/10.2.0/snos` | Use the GCC 10.2.0 toolchain instead of the default 8.1.0 version packaged with CCE |

Performance options:

| Option | Comment |  
| ------ | ------- |
| `-Ofast` | Optimisation levels: -O0, -O1, -O2, -O3, -Ofast            |
| `-ffp=level` | Floating point maths optimisations levels 0-4 \[2\]    |
| `-flto` | Link time optimisation                                      |


Miscellaneous options:

| Option | Comment |  
| ------ | ------- |
| `-fopenmp` | Compile OpenMP (default is off)                          |
| `-v` | Display verbose output from compiler stages                    |

!!! notes
    1.  Option `-std=gnu11` gives `c11` plus GNU extensions (likewise
        `c++14` plus GNU extensions). See
        <https://gcc.gnu.org/onlinedocs/gcc-4.8.2/gcc/C-Extensions.html>
    2.  Option `-ffp=3` is implied by `-Ofast` or `-ffast-math`

#### Useful CCE Fortran options

Language, Warning, Debugging options:

| Option | Comment |  
| ------ | ------- |
| `-m <level>` | Message level (default `-m 3` errors and warnings) |

Performance options:

| Option | Comment |  
| ------ | ------- |
| `-O <level>` | Optimisation levels: -O0 to -O3 (default -O2)      |
| `-h fp<level>` | Floating point maths optimisations levels 0-3    |
| `-h ipa` | Inter-procedural analysis                              |

Miscellaneous options:

| Option | Comment |  
| ------ | ------- |
| `-h omp` | Compile OpenMP (default is `-hnoomp`)                  |
| `-v` | Display verbose output from compiler stages                |

#### CCE Reference Documentation

* [Clang/Clang++ documentation](https://clang.llvm.org/docs/UsersManual.html), CCE-specific 
  details are available via `man clang` once the CCE compiler environment is loaded.
* [Cray Fortran documentation](https://internal.support.hpe.com/hpesc/public/docDisplay?docId=a00114872en_us&page=index.html)

### AMD Optimizing Compiler Collection (AOCC)

The AMD Optimizing Compiler Collection (AOCC) is a clang-based optimising
compiler. AOCC also includes a flang-based Fortran compiler.

Switch the the AOCC compiler environment from the default CCE (cray)
compiler environment via:

```
auser@ln03:~> module swap PrgEnv-cray PrgEnv-aocc

Due to MODULEPATH changes, the following have been reloaded:
  1) cray-mpich/8.1.4

```

!!! note
    Further details on AOCC will appear here as they become available.

#### AOCC reference material

  - AMD website  
    <https://developer.amd.com/amd-aocc/>


## Message passing interface (MPI)

### HPE Cray MPICH

HPE Cray provide, as standard, an MPICH implementation of the message
passing interface which is specifically optimised for the ARCHER2
interconnect. The current implementation supports MPI standard version 3.1.

The HPE Cray MPICH implementation is linked into software by default when
compiling using the standard wrapper scripts: `cc`, `CC` and `ftn`.

You do not need to do anything to make HPE Cray MPICH available when you
log into ARCHER2, it is available by default to all users.

#### Switching to alternative UCX MPI implementation

HPE Cray MPICH can use two different low-level protocols to transfer
data across the network. The default is the Open Fabrics Interface
(OFI), but you can switch to the UCX protocol from Mellanox.

Which performs better will be application-dependent, but our
experience is that UCX is often faster for programs that send a lot of
data collectively between many processes, e.g. all-to-all
communications patterns such as occur in parallel FFTs.


!!! note
       You do not need to recompile your program - you simply load
       different modules in your Slurm script.

```
module swap craype-network-ofi craype-network-ucx 
module swap cray-mpich cray-mpich-ucx 
```

The performance benefits will also vary depending on the number of
processes, so it is important to benchmark your application at the
scale used in full production runs.

#### MPI reference material

MPI standard documents: <https://www.mpi-forum.org/docs/>

## Linking and libraries

Linking to libraries is performed dynamically on ARCHER2.

!!! important
    Static linking is not supported on ARCHER2. If you attempt to link statically,
    you will see errors similar to:
    ```
    /usr/bin/ld: cannot find -lpmi
    /usr/bin/ld: cannot find -lpmi2
    collect2: error: ld returned 1 exit status
    ```

One can use the `-craype-verbose` flag to the compiler wrapper to check exactly what
linker arguments are invoked. The compiler wrapper scripts encode the
paths to the programming environment system libraries using RUNPATH.
This ensures that the executable can find the correct runtime
libraries without the matching software modules loaded.

!!! tip
    The RUNPATH setting in the executable only works for default versions
    of libraries. If you want to use non-default versions then you need
    to add some additional commands at compile time and in your job submission
    scripts. See the [Using non-default versions of HPE Cray libraries on ARCHER2](#using-non-default-versions-of-hpe-cray-libraries-on-archer2).
    
The library RUNPATH associated with an executable can be inspected via,
e.g.,

    $ readelf -d ./a.out

(swap `a.out` for the name of the executable you are querying).

### Commonly used libraries

Modules with names prefixed by `cray-` are provided by HPE Cray, and work
with any of the compiler environments and. These modules should be the
first choice for access to software libraries if available.

!!! tip
    More information on the different software libraries on ARCHER2 can
    be found in the [Software libraries](../../software-libraries/)
    section of the user guide.

## Switching to a different HPE Cray Programming Environment release

!!! important
    See the section below on using non-default versions of HPE Cray libraries
    as this process will generally need to be followed when using software
    from non-default PE installs.

Access to non-default PE environments is controlled by the use of the `cpe` modules.
Loading a `cpe` module will do the following:

- The compiler version will be switched to the one from the selected PE
- All HPE Cray PE modules will be updated so their default version is the
  one from the PE you have selected

For example, if you have a code that uses the Gnu compiler environment, FFTW
and NetCDF parallel libraries and you want to compile in the (non-default) 21.09
programming environment, you would do the following:

First, load the `cpe/21.09` module to switch all the defaults to the versions from
the 21.09 PE. Then, swap to the Gnu compiler environment and load the required library
modules (FFTW, hdf5-parallel and NetCDF HDF5 parallel). The loaded module list shows they 
are the versions from the 21.09 PE:


```
auser@uan02:/work/t01/t01/auser> module load cpe/21.09

The following have been reloaded with a version change:
  1) PrgEnv-cray/8.0.0 => PrgEnv-cray/8.1.0     2) cce/11.0.4 => cce/12.0.3     3) cray-libsci/21.04.1.1 => cray-libsci/21.08.1.2     4) cray-mpich/8.1.4 => cray-mpich/8.1.9     5) craype/2.7.6 => craype/2.7.10

auser@uan02:/work/t01/t01/auser> module swap PrgEnv-cray PrgEnv-gnu

Due to MODULEPATH changes, the following have been reloaded:
  1) cray-mpich/8.1.9

auser@uan02:/work/t01/t01/auser> module load cray-fftw
auser@uan02:/work/t01/t01/auser> module load cray-hdf5-parallel
auser@uan02:/work/t01/t01/auser> module load cray-netcdf-hdf5parallel
auser@uan02:/work/t01/t01/auser> module list

Currently Loaded Modules:
  1) cpe/21.09         5) libfabric/1.11.0.4.71   9) cray-libsci/21.08.1.2  13) PrgEnv-gnu/8.1.0
  2) gcc/11.2.0        6) craype-network-ofi     10) bolt/0.7               14) cray-fftw/3.3.8.11
  3) craype/2.7.10     7) cray-dsmml/0.2.1       11) epcc-setup-env         15) cray-hdf5-parallel/1.12.0.7
  4) craype-x86-rome   8) cray-mpich/8.1.9       12) load-epcc-module       16) cray-netcdf-hdf5parallel/4.7.4.7


```

Finally, you will need to modify the value of
`LD_LIBRARY_PATH` before you compile your software to ensure it picks up the
non-default versions of libraries:

```
auser@uan02:/work/t01/t01/auser> export LD_LIBRARY_PATH=$CRAY_LD_LIBRARY_PATH:$LD_LIBRARY_PATH
```

Now you can go ahead and compile your software with the new programming
environment.

!!! important
    The `cpe` modules only change the versions of software modules provided
    as part of the HPE Cray programming environments. Any modules provided
    by the ARCHER2 service will need to be loaded manually after you have
    completed the process described above.
    
!!! note
    Unloading the `cpe` module does not restore the original programming environment
    release. To restore the default programming environment release you should log 
    out and then log back in to ARCHER2.

#### Accessing performance analysis tools in non-default Programming Environment

The performance analysis tools (such as CrayPAT and CrayPAT-lite) behave slightly differently
to other HPE Cray modules when you change to a non-default version of the programming
environment. Specifically, an additional step is required to make them available. Once
you have loaded the `cpe` module, you will also need to load the `perftools-base` 
module to be able to load and use the performance tools modules.
    
### Available HPE Cray Programming Environment releases on ARCHER2

ARCHER2 currently has the following HPE Cray Programming Environment releases available:

- **21.04: Current default**
- 21.09: available via `cpe/21.09` module

You can find information, notes, and lists of changes for current and upcoming ARCHER2 
HPE Cray programming environments in [the HPE Cray Programming Environment GitHub
repository](https://github.com/PE-Cray).

## Using non-default versions of HPE Cray libraries on ARCHER2

If you wish to make use of non-default versions of libraries provided by HPE
Cray (usually because they are part of a non-default PE release: either old
or new) then you need to make changes at *both* compile and runtime. In summary,
you need to load the correct module and also make changes to the `LD_LIBRARY_PATH`
environment variable.

**At compile time** you need to load the version of the library module before you compile
*and* set the LD_LIBRARY_PATH environment variable to include the contencts of
`$CRAY_LD_LIBRARY_PATH` as the first entry. For example, to use the, non-default, 21.09.1.2
version of HPE Cray LibSci in the default programming environment (Cray Compiler Environment,
CCE) you would first setup the environment to compile with:

```
auser@uan01:~/test/libsci> module swap cray-libsci cray-libsci/21.08.1.2
auser@uan01:~/test/libsci> export LD_LIBRARY_PATH=$CRAY_LD_LIBRARY_PATH:$LD_LIBRARY_PATH
```

The order is important here: every time you change a module, you will need to reset
the value of `LD_LIBRARY_PATH` for the process to work (it will not be updated
automatically).

Now you can compile your code. You can check that the executable is using the correct version 
of LibSci with the `ldd` command and look for the line beginning `libsci_cray.so.5`, you
should see the version in the path to the library file:

```
auser@uan01:~/test/libsci> ldd dgemv.x 
	linux-vdso.so.1 (0x00007fffd33dd000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fbd6e7ed000)
	libsci_cray.so.5 => /opt/cray/pe/libsci/21.08.1.2/CRAY/9.0/x86_64/lib/libsci_cray.so.5 (0x00007fbd6a8a7000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fbd6a6a3000)
	libxpmem.so.0 => /opt/cray/xpmem/default/lib64/libxpmem.so.0 (0x00007fbd6a4a0000)
	libquadmath.so.0 => /opt/cray/pe/cce/11.0.4/cce/x86_64/lib/libquadmath.so.0 (0x00007fbd6a260000)
	libmodules.so.1 => /opt/cray/pe/cce/11.0.4/cce/x86_64/lib/libmodules.so.1 (0x00007fbd6a044000)
	libfi.so.1 => /opt/cray/pe/cce/11.0.4/cce/x86_64/lib/libfi.so.1 (0x00007fbd69921000)
	libcraymath.so.1 => /opt/cray/pe/cce/11.0.4/cce/x86_64/lib/libcraymath.so.1 (0x00007fbd69640000)
	libf.so.1 => /opt/cray/pe/cce/11.0.4/cce/x86_64/lib/libf.so.1 (0x00007fbd693ac000)
	libu.so.1 => /opt/cray/pe/cce/11.0.4/cce/x86_64/lib/libu.so.1 (0x00007fbd69098000)
	libcsup.so.1 => /opt/cray/pe/cce/11.0.4/cce/x86_64/lib/libcsup.so.1 (0x00007fbd68e92000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fbd68ad7000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fbd6eb25000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fbd688b8000)
	librt.so.1 => /lib64/librt.so.1 (0x00007fbd686b0000)
	libgfortran.so.5 => /opt/cray/pe/gcc-libs/libgfortran.so.5 (0x00007fbd681f9000)
	libstdc++.so.6 => /opt/cray/pe/gcc-libs/libstdc++.so.6 (0x00007fbd67e26000)
	libgcc_s.so.1 => /opt/cray/pe/gcc-libs/libgcc_s.so.1 (0x00007fbd67c0e000
```

!!! tip
    If any of the libraries point to versions in the `/opt/cray/pe/lib64` directory
    then these are using the default versions of the libraries rather than the 
    specific versions. This happens at compile time if you have forgotton to load 
    the right module and set `$LD_LIBRARY_PATH` afterwards.

**At run time** (typically in your job script) you need to repeat the environment
setup steps (you can also use the `ldd` command in your job submission script to 
check the library is pointing to the correct version). For example, a job submission
script to run our `dgemv.x` executable with the non-default version of LibSci could
look like:

```
#!/bin/bash
#SBATCH --job-name=dgemv
#SBATCH --time=0:20:0
#SBATCH --nodes=1
#SBATCH --tasks-per-node=1
#SBATCH --cpus-per-task=1

# Replace the account code, partition and QoS with those you wish to use
#SBATCH --account=t01        
#SBATCH --partition=standard
#SBATCH --qos=short
#SBATCH --reservation=shortqos

# Setup up the environment to use the non-default version of LibSci
#   We use "module swap" as the "cray-libsci" is loaded by default.
#   This must be done after loading the "epcc-job-env" module
module swap cray-libsci cray-libsci/20.08.1.2
export LD_LIBRARY_PATH=$CRAY_LD_LIBRARY_PATH:$LD_LIBRARY_PATH

# Check which library versions the executable is pointing too
ldd dgemv.x

export OMP_NUM_THREADS=1

srun --hint=nomultithread --distribution=block:block dgemv.x
```

!!! tip
    As when compiling, the order of commands matters. Setting the value of
    `LD_LIBRARY_PATH` must happen after you have finished all your `module`
    commands for it to have the correct effect.

!!! important
    You must setup the environment at both compile and run time otherwise
    you will end up using the default version of the library.

## Compiling on compute nodes

Sometimes you may wish to compile in a batch job. For example, the compile process may take a long
time or the compile process is part of the research workflow and can be coupled to the production job.
Unlike login nodes, the `/home` file system is not available.

An example job submission script for a compile job using `make` (assuming the Makefile is in the same
directory as the job submission script) would be:

```
#!/bin/bash

#SBATCH --job-name=compile
#SBATCH --time=00:20:00
#SBATCH --nodes=1
#SBATCH --tasks-per-node=1
#SBATCH --cpus-per-task=1

# Replace the account code, partition and QoS with those you wish to use
#SBATCH --account=t01        
#SBATCH --partition=standard
#SBATCH --qos=standard


make clean

make
```

!!! note
    If you want to use a compiler environment other than the default then
    you will need to add the `module swap` command before the `make` command.
    e.g. to use the Gnu compiler environemnt:
    
    ```
    module swap PrgEnv-cray PrgEnv-gnu
    ```

You can also use a compute node in an interactive way using `salloc`. Please see
Section [Using salloc to reserve resources](../scheduler/#using-salloc-to-reserve-resources)
for further details. Once your interactive session is ready, you can load the compilation environment and compile the code.

## Managing development

ARCHER2 supports common revision control software such as `git`.

Standard GNU autoconf tools are available, along with `make` (which is
GNU Make). Versions of `cmake` are available.

!!! tip
    Some of these tools are part of the system software, and
    typically reside in `/usr/bin`, while others are provided as part of the
    module system. Some tools may be available in different versions via
    both `/usr/bin` and via the module system. If you find the default
    version is too old, then look in the module system for a more recent
    version.

## Build instructions for software on ARCHER2

The ARCHER2 CSE team at [EPCC](https://www.epcc.ed.ac.uk) and other contributors
provide build configurations ando instructions for a range of research
software, software libraries and tools on a variety of HPC systems (including
ARCHER2) in a public Github repository. See:

   - [Build instructions repository](https://www.github.com/HPC-UK/build-instructions)

The repository always welcomes contributions from the ARCHER2 user community.

## Support for building software on ARCHER2

If you run into issues building software on ARCHER2 or the software you
require is not available then please contact the
[ARCHER2 Service Desk](https://www.archer2.ac.uk/support-access/servicedesk.html)
with any questions you have.
