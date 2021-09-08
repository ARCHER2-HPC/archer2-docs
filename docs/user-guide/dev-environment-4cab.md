# Application development environment: 4-cabinet system

!!! important
    This section covers the application development environment on the initial, 4-cabinet
    ARCHER2 system. For docmentation on the application development environment on the
    full ARCHER2 system, please see [Application development environment: full system](sw-environment.md).

## What's available

ARCHER2 runs on the Cray Linux Environment (a version of SUSE Linux),
and provides a development environment which includes:

  - Software modules via a standard module framework
  - Three different compiler environments (AMD, Cray, and GNU)
  - MPI, OpenMP, and SHMEM
  - Scientific and numerical libraries
  - Parallel Python and R
  - Parallel debugging and profiling
  - Singularity containers

Access to particular software, and particular versions, is managed by a
standard TCL module framework. Most software is available via standard
software modules and the different programming environments are
available via module collections.

You can see what programming environments are available with:

    auser@uan01:~> module savelist
    Named collection list:
     1) PrgEnv-aocc   2) PrgEnv-cray   3) PrgEnv-gnu 

Other software modules can be listed with

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

A full discussion of the module system is available in
[the Software environment section](sw-environment.md).

A consistent set of modules is loaded on login to the machine (currently
`PrgEnv-cray`, see below). Developing applications then means selecting
and loading the appropriate set of modules before starting work.

This section is aimed at code developers and will concentrate on the
compilation environment and building libraries and executables, and
specifically parallel executables. Other topics such as [Python](python.md) and
[Containers](containers.md) are covered in more detail in separate sections of the
documentation.

## Managing development

ARCHER2 supports common revision control software such as `git`.

Standard GNU autoconf tools are available, along with `make` (which is
GNU Make). Versions of `cmake` are available.

!!! note
    Some of these tools are part of the system software, and
    typically reside in `/usr/bin`, while others are provided as part of the
    module system. Some tools may be available in different versions via
    both `/usr/bin` and via the module system.

## Compilation environment

There are three different compiler environments available on ARCHER2:
AMD (AOCC), Cray (CCE), and GNU (GCC). The current compiler suite is selected via the
programming environment, while the specific compiler versions are
determined by the relevant compiler module. A summary is:

| Suite name | Module | Programming environment collection |
| ---------- | ------ | ---------------------------------- |
| CCE        | `cce`  | `PrgEnv-cray`                      |
| GCC        | `gcc`  | `PrgEnv-gnu`                       |
| AOCC       | `aocc` | `PrgEnv-aocc`                      |

For example, at login, the default set of modules are:

``` 
Currently Loaded Modulefiles:
1) cpe-cray                          7) cray-dsmml/0.1.2(default)                           
2) cce/10.0.3(default)               8) perftools-base/20.09.0(default)                     
3) craype/2.7.0(default)             9) xpmem/2.2.35-7.0.1.0_1.3__gd50fabf.shasta(default)  
4) craype-x86-rome                  10) cray-mpich/8.0.15(default)                          
5) libfabric/1.11.0.0.233(default)  11) cray-libsci/20.08.1.2(default)                      
6) craype-network-ofi  
```

from which we see the default programming environment is Cray (indicated
by `cpe-cray` (at 1 in the list above) and the default compiler module
is `cce/10.0.3` (at 2 in the list above). The programming environment
will give access to a consistent set of compiler, MPI library via
`cray-mpich` (at 10), and other libraries e.g., `cray-libsci` (at 11 in
the list above) infrastructure.

Within a given programming environment, it is possible to swap to a
different compiler version by swapping the relevant compiler module.

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
be used via the appropriate module. Other common MPI compiler wrappers
e.g., `mpicc` should also be replaced by the relevant wrapper `cc`
(`mpicc` etc are not available).

!!! important
    Always use the compiler wrappers `cc`, `CC`, and/or `ftn` and not a
    specific compiler invocation. This will ensure consistent compile/link
    time behaviour.

### Compiler man pages and help

Further information on both the compiler wrappers, and the individual
compilers themselves are available via the command line, and via
standard `man` pages. The `man` page for the compiler wrappers is common
to all programming environments, while the `man` page for individual
compilers depends on the currently loaded programming environment. The
following table summarises options for obtaining information on the
compiler and compile options:

| Compiler suite | C            | C++          | Fortran        |
| -------------- | ------------ | ------------ | -------------- |
| Cray           | `man craycc` | `man crayCC` | `man crayftn`  |
| GNU            | `man gcc`    | `man g++`    | `man gfortran` |
| Wrappers       | `man cc`     | `man CC`     | `man ftn`      |

!!! tip
    You can also pass the `--help` option to any of the compilers or
    wrappers to get a summary of how to use them. The Cray Fortran
    compiler uses `ftn --craype-help` to access the help options.

!!! tip
    There are no `man` pages for the AOCC compilers at the moment.

!!! tip
    Cray C/C++ is based on Clang and therefore
    supports similar options to clang/gcc (`man clang` is in fact equivalent
    to `man craycc`). `clang --help` will produce a full summary
    of options with Cray-specific options marked "Cray". The `craycc` man
    page concentrates on these Cray extensions to the `clang` front end and
    does not provide an exhaustive description of all `clang` options.
    Cray Fortran **is not** based on Flang and so takes different options
    from flang/gfortran.

### Dynamic Linking

Executables on ARCHER2 link dynamically, and the Cray Programming
Environment does not currently support static linking. This is in
contrast to ARCHER where the default was to build statically.

If you attempt to link statically, you will see errors similar to:

    /usr/bin/ld: cannot find -lpmi
    /usr/bin/ld: cannot find -lpmi2
    collect2: error: ld returned 1 exit status

The compiler wrapper scripts on ARCHER link runtime libraries in using
the `runpath` by default. This means that the paths to the runtime
libraries are encoded into the executable so you do not need to load the
compiler environment in your job submission scripts.

### Which compiler environment?

If you are unsure which compiler you should choose, we suggest the
starting point should be the GNU compiler collection (GCC,
`PrgEnv-gnu`); this is perhaps the most commonly used by code
developers, particularly in the open source software domain. A portable,
standard-conforming code should (in principle) compile in any of the
three programming environments.

For users requiring specific compiler features, such as co-array
Fortran, the recommended starting point would be Cray. The following
sections provide further details of the different programming
environments.

!!! warning
    Intel compilers are not available on ARCHER2.

### AMD Optimizing C/C++ Compiler (AOCC)

The AMD Optimizing C/++ Compiler (AOCC) is a clang-based optimising
compiler. AOCC (despite its name) includes a flang-based Fortran
compiler.

Switch the the AOCC programming environment via

    $ module restore PrgEnv-aocc

!!! note
    Further details on AOCC will appear here as they become available.

#### AOCC reference material

  - AMD website  
    <https://developer.amd.com/amd-aocc/>

### Cray compiler environment (CCE)

The Cray compiler environment (CCE) is the default compiler at the point
of login. CCE supports C/C++ (along with unified parallel C UPC), and
Fortran (including co-array Fortran). Support for OpenMP parallelism is
available for both C/C++ and Fortran (currently OpenMP 4.5, with a
number of exceptions).

The Cray C/C++ compiler is based on a clang front end, and so compiler
options are similar to those for gcc/clang. However, the Fortran
compiler remains based around Cray-specific options. Be sure to separate
C/C++ compiler options and Fortran compiler options (typically `CFLAGS`
and `FFLAGS`) if compiling mixed C/Fortran applications.

Switch the the Cray programming environment via

    $ module restore PrgEnv-cray

#### Useful CCE C/C++ options

When using the compiler wrappers `cc` or `CC`, some of the following
options may be
useful:

Language, warning, Debugging options:

| Option | Comment |  
| ------ | ------- |
| `-std=<standard>` | Default is `-std=gnu11` (`gnu++14` for C++) \[1\] |


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

### GNU compiler collection (GCC)

The commonly used open source GNU compiler collection is available and
provides C/C++ and Fortran compilers.

The GNU compiler collection is loaded by switching to the GNU
programming environment:

    $ module restore PrgEnv-gnu

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

## Message passing interface (MPI)

### HPE Cray MPICH

HPE Cray provide, as standard, an MPICH implementation of the message
passing interface which is specifically optimised for the ARCHER2
network. The current implementation supports MPI standard version 3.1.

The HPE Cray MPICH implementation is linked into software by default when
compiling using the standard wrapper scripts: `cc`, `CC` and `ftn`.

#### MPI reference material

MPI standard documents: <https://www.mpi-forum.org/docs/>

## Linking and libraries

Linking to libraries is performed dynamically on ARCHER2. One can use
the `-craype-verbose` flag to the compiler wrapper to check exactly what
linker arguments are invoked. The compiler wrapper scripts encode the
paths to the programming environment system libraries using RUNPATH.
This ensures that the executable can find the correct runtime
libraries without the matching software modules loaded.

The library RUNPATH associated with an executable can be inspected via,
e.g.,

    $ readelf -d ./a.out

(swap `a.out` for the name of the executable you are querying).

### Commonly used libraries

Modules with names prefixed by `cray-` are provided by HPE Cray, and are
supported to be consistent with any of the programming environments and
associated compilers. These modules should be the first choice for
access to software libraries if available.

!!! tip
    More information on the different software libraries on ARCHER2 can
    be found in the [Software libraries](../software-libraries/)
    section of the user guide.

## Switching to a different HPE Cray Programming Environment release

!!! important
    See the section below on using non-default versions of HPE Cray libraries
    below as this process will generally need to be followed when using software
    from non-default PE installs.

Access to non-default PE environments is controlled by the use of the `cpe` modules.
These modules are typically loaded *after* you have restored a PrgEnv and loaded all
the other modules you need and will 
set your compile environment to match that in the other PE release. This means:

- The compiler version will be switched to the one from the selected PE
- HPE Cray provided libraries (or modules) that are loaded before you switch
  to the new programming environment are switched to those from the programming
  environment that you select.


For example, if you have a code that uses the Gnu programming environment, FFTW
and NetCDF parallel libraries and you want to compile in the (non-default) 21.03
programming environment, you would do the following:

First, restore the Gnu programming environment and load the required library
modules (FFTW and NetCDF HDF5 parallel). The loaded module list shows they 
are the versions from the default (20.10) programming environment):


```
auser@uan02:/work/t01/t01/auser> module restore -s PrgEnv-gnu
auser@uan02:/work/t01/t01/auser> module load cray-fftw
auser@uan02:/work/t01/t01/auser> module load cray-netcdf
auser@uan02:/work/t01/t01/auser> module load cray-netcdf-hdf5parallel
auser@uan02:/work/t01/t01/auser> module list
Currently Loaded Modulefiles:
 1) cpe-gnu                           9) xpmem/2.2.35-7.0.1.0_1.9__gd50fabf.shasta(default)               
 2) gcc/10.1.0(default)              10) cray-mpich/8.0.16(default)                                       
 3) craype/2.7.2(default)            11) cray-libsci/20.10.1.2(default)                                   
 4) craype-x86-rome                  12) bolt/0.7                                                         
 5) libfabric/1.11.0.0.233(default)  13) /work/y07/shared/archer2-modules/modulefiles-cse/epcc-setup-env  
 6) craype-network-ofi               14) /usr/local/share/epcc-module/epcc-module-loader                  
 7) cray-dsmml/0.1.2(default)        15) cray-fftw/3.3.8.8(default)                                       
 8) perftools-base/20.10.0(default)  16) cray-netcdf-hdf5parallel/4.7.4.2(default) 
```

Now, load the `cpe/21.03` programming environment module to switch all
the currently loaded HPE Cray modules from the default (20.10) programming
environment version to the 21.03 programming environment versions:

```
auser@uan02:/work/t01/t01/auser> module load cpe/21.03
Switching to cray-dsmml/0.1.3.
Switching to cray-fftw/3.3.8.9.
Switching to cray-libsci/21.03.1.1.
Switching to cray-mpich/8.1.3.
Switching to cray-netcdf-hdf5parallel/4.7.4.3.
Switching to craype/2.7.5.
Switching to gcc/9.3.0.
Switching to perftools-base/21.02.0.

Loading cpe/21.03
  Unloading conflict: cray-dsmml/0.1.2 cray-fftw/3.3.8.8 cray-libsci/20.10.1.2 cray-mpich/8.0.16 cray-netcdf-hdf5parallel/4.7.4.2
    craype/2.7.2 gcc/10.1.0 perftools-base/20.10.0
  Loading requirement: cray-dsmml/0.1.3 cray-fftw/3.3.8.9 cray-libsci/21.03.1.1 cray-mpich/8.1.3 cray-netcdf-hdf5parallel/4.7.4.3
    craype/2.7.5 gcc/9.3.0 perftools-base/21.02.0
auser@uan02:/work/t01/t01/auser> module list
Currently Loaded Modulefiles:
 1) cpe-gnu                                                           9) cray-dsmml/0.1.3                  17) cpe/21.03(default)  
 2) craype-x86-rome                                                  10) cray-fftw/3.3.8.9                 
 3) libfabric/1.11.0.0.233(default)                                  11) cray-libsci/21.03.1.1             
 4) craype-network-ofi                                               12) cray-mpich/8.1.3                  
 5) xpmem/2.2.35-7.0.1.0_1.9__gd50fabf.shasta(default)               13) cray-netcdf-hdf5parallel/4.7.4.3  
 6) bolt/0.7                                                         14) craype/2.7.5                      
 7) /work/y07/shared/archer2-modules/modulefiles-cse/epcc-setup-env  15) gcc/9.3.0                         
 8) /usr/local/share/epcc-module/epcc-module-loader                  16) perftools-base/21.02.0   
```

Finally (as noted above), you will need to modify the value of
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
    
!!! bug
    The `cpe/21.03` module has a known issue with `PrgEnv-gnu` where it loads an old version
    of GCC (9.3.0) rather than the correct, newer version (10.2.0). You can resolve this by
    using the sequence:
    ```
    module restore -s PrgEnv-gnu
    ...load any other modules you need...
    module load cpe/21.03
    module unload cpe/21.03
    module swap gcc gcc/10.2.0
    ```
    
### Available HPE Cray Programming Environment releases on ARCHER2

ARCHER2 currently has the following HPE Cray Programming Environment releases available:

- 20.08: not available via `cpe` module
- **20.10: Current default**
- 21.03: available via `cpe/21.03` module

!!! tip
    You can see which programming environment release you currently have loaded 
    by using `module list` and looking at the version number of the `cray-libsci`
    module you have loaded. The first two numbers indicate the version of the
    PE you have loaded. For example, if you have `cray-libsci/20.10.1.2` loaded
    then you are using the 20.10 PE release.

## Using non-default versions of HPE Cray libraries on ARCHER2

If you wish to make use of non-default versions of libraries provided by HPE
Cray (usually because they are part of a non-default PE release: either old
or new) then you need to make changes at *both* compile and runtime. In summary,
you need to load the correct module and also make changes to the `LD_LIBRARY_PATH`
environment variable.

**At compile time** you need to load the version of the library module before you compile
*and* set the LD_LIBRARY_PATH environment variable to include the contencts of
`$CRAY_LD_LIBRARY_PATH` as the first entry. For example, to use the, non-default, 20.08.1.2
version of HPE Cray LibSci in the default programming environment (Cray Compiler Environment,
CCE) you would first setup the environment to compile with:

```
auser@uan01:~/test/libsci> module swap cray-libsci cray-libsci/20.08.1.2 
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
	linux-vdso.so.1 (0x00007ffe4a7d2000)
	libsci_cray.so.5 => /opt/cray/pe/libsci/20.08.1.2/CRAY/9.0/x86_64/lib/libsci_cray.so.5 (0x00007fafd6a43000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fafd683f000)
	libxpmem.so.0 => /opt/cray/xpmem/default/lib64/libxpmem.so.0 (0x00007fafd663c000)
	libquadmath.so.0 => /opt/cray/pe/cce/10.0.4/cce/x86_64/lib/libquadmath.so.0 (0x00007fafd63fc000)
	libmodules.so.1 => /opt/cray/pe/cce/10.0.4/cce/x86_64/lib/libmodules.so.1 (0x00007fafd61e0000)
	libfi.so.1 => /opt/cray/pe/cce/10.0.4/cce/x86_64/lib/libfi.so.1 (0x00007fafd5abe000)
	libcraymath.so.1 => /opt/cray/pe/cce/10.0.4/cce/x86_64/lib/libcraymath.so.1 (0x00007fafd57e2000)
	libf.so.1 => /opt/cray/pe/cce/10.0.4/cce/x86_64/lib/libf.so.1 (0x00007fafd554f000)
	libu.so.1 => /opt/cray/pe/cce/10.0.4/cce/x86_64/lib/libu.so.1 (0x00007fafd523b000)
	libcsup.so.1 => /opt/cray/pe/cce/10.0.4/cce/x86_64/lib/libcsup.so.1 (0x00007fafd5035000)
	libstdc++.so.6 => /opt/cray/pe/gcc-libs/libstdc++.so.6 (0x00007fafd4c62000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fafd4a43000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fafd4688000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fafd4350000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fafda988000)
	librt.so.1 => /lib64/librt.so.1 (0x00007fafd4148000)
	libgfortran.so.5 => /opt/cray/pe/gcc-libs/libgfortran.so.5 (0x00007fafd3c92000)
	libgcc_s.so.1 => /opt/cray/pe/gcc-libs/libgcc_s.so.1 (0x00007fafd3a7a000)
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

# Load the standard environment module
module load epcc-job-env

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

## Compiling in compute nodes

Sometimes you may wish to compile in a batch job. For example, the compile process may take a long time or the compile process is part of the research workflow and can be coupled to the production job. Unlike login nodes, the `/home` file system is not available.

An example job submission script for a compile job using `make` (assuming the Makefile is in the same directory as the job submission script) would be:

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

# Load the compilation environment (cray, gnu or aocc)
module restore /etc/cray-pe.d/PrgEnv-cray

make clean

make
```

!!! warning
    Do not forget to include the full path when the
    compilation environment is restored. For instance:
    
    `module restore /etc/cray-pe.d/PrgEnv-cray`

You can also use a compute node in an interactive way using `salloc`. Please see
Section [Using salloc to reserve resources](../scheduler/#using-salloc-to-reserve-resources)
for further details. Once your interactive session is ready, you can load the compilation environment and compile the code.

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
