# Quickstart for developers

This guide aims to quickly enable developers to work on ARCHER2. It
assumes that you are familiar with the material in the
[Quickstart for users](quickstart-users.md) section.

## Compiler wrappers

When compiling code on ARCHER2, you should make use of the HPE Cray compiler
wrappers. These ensure that the correct libraries and headers (for
example, MPI or HPE LibSci) will be used during the compilation and
linking stages. These wrappers should be accessed by providing the
following compiler names:

| Language | Wrapper name |
| -------- | ------------ |
| C        | cc           |
| C++      | CC           |
| Fortran  | ftn          |

This means that you should use the wrapper names whether on the command
line, in build scripts, or in configure options. It could be helpful to
set some or all of the following environment variables before running a
build to ensure that the build tool is aware of the wrappers:

  export CC=cc
  export CXX=CC
  export FC=ftn
  export F77=ftn
  export F90=ftn

`man` pages are available for each wrapper. You can also see the full
set of compiler and linker options being used by passing the
`-craype-verbose` option to the wrapper when using it.

!!! tip
    The HPE Cray compiler wrappers should be used instead of the MPI compiler
    wrappers such as `mpicc`, `mpicxx` and `mpif90` that you may have used
    on other HPC systems.


## Programming environments

On login to ARCHER2, the `PrgEnv-cray` compiler environment will be loaded, as
will a `cce` module. The latter makes available the Cray compilers from
the Cray Compiling Environment (CCE), while the former provides the
correct wrappers and support to use them. The GNU Compiler Collection
(GCC) and the AMD compiler environment (AOCC) are also available.

To make use of any particular compiler environment, you load the correct `PrgEnv`
module. After doing so the compiler wrappers (`cc`, `CC` and `ftn`)
will correctly call the compilers from the new suite. The default
version of the corresponding compiler suite will also be loaded, but you
may swap to another available version if you wish.

The following table summarises the suites and associated compiler
environments.

| Suite name | Module | Programming environment collection |
| ---------- | ------ | ---------------------------------- |
| CCE        | `cce`  | `PrgEnv-cray`                      |
| GCC        | `gcc`  | `PrgEnv-gnu`                       |
| AOCC       | `aocc` | `PrgEnv-aocc`                      |

As an example, after logging in you may wish to use GCC as your compiler
suite. Running `module load PrgEnv-gnu` will replace the default CCE (Cray)
environment with the GNU environment. It will also unload the `cce`
module and load the default version of the `gcc` module; at the time of
writing, this is GCC 10.2.0. If you need to use a different version of
GCC, for example 9.3.0, you would follow up with `module swap gcc
gcc/9.3.0`. At this point you may invoke the compiler wrappers and they
will correctly use the HPE libraries and tools in conjunction with GCC
9.3.0.

!!! warning
    The `gcc/8.1.0` module
    is available on ARCHER2 but cannot be used as the
    supporting scientific and system libraries are not available. You should
    **not** use this version of GCC.

When choosing the compiler environment, a big factor will likely be
which compilers you have previously used for your code's development.
The Cray Fortran compiler is similar to the compiler you may be familiar
with from ARCHER, while the Cray C and C++ compilers provided on ARCHER2
are new versions that are now derived from Clang. The GCC suite provides
gcc/g++ and gfortran. The AOCC suite provides AMD Clang/Clang++ and AMD
Flang.

!!! note 
    The Intel compilers are not available on ARCHER2.

## Useful compiler options

The compiler options you use will depend on both the software you are
building and also on the current stage of development. The following
flags should be a good starting point for reasonable performance:

| Compilers    | Optimisation flags                                |
| ------------ | ------------------------------------------------- |
| Cray C/C++   | `-O2 -funroll-loops -ffast-math`                  |
| Cray Fortran | Default options                                   |
| GCC          | `-O2 -ftree-vectorize -funroll-loops -ffast-math` |

!!! tip
    If you want to use GCC version 10 or greater to compile MPI Fortran code,
    you **must** add the `-fallow-argument-mismatch` option when compiling
    otherwise you will see compile errors associated with MPI functions.

When you are happy with your code's performance you may wish to enable
more aggressive optimisations; in this case you could start using the
following flags. Please note, however, that these optimisations may lead
to deviations from IEEE/ISO specifications. If your code relies on
strict adherence then these flags may lead to it producing incorrect
output.

| Compilers    | Optimisation flags      |
| ------------ | ----------------------- |
| Cray C/C++   | `-Ofast -funroll-loops` |
| Cray Fortran | `-O3 -hfp3`             |
| GCC          | `-Ofast -funroll-loops` |

Vectorisation is enabled by the Cray Fortran compiler at `-O1` and
above, by Cray C and C++ at `-O2` and above or when using
`-ftree-vectorize`, and by the GCC compilers at `-O3` and above or when
using `-ftree-vectorize`.

You may wish to promote default `real` and `integer` types in Fortran
codes from 4 to 8 bytes. In this case, the following flags may be used:

| Compiler     | Fortran `real` and `integer` promotion flags |
| ------------ | -------------------------------------------- |
| Cray Fortran | `-s real64 -s integer64`                     |
| gfortran     | `-freal-4-real-8 -finteger-4-integer-8`      |

More documentation on the compilers is available through `man`. The
pages to read are accessed as follow:

| Compiler suite | C            | C++          | Fortran        |
| -------------- | ------------ | ------------ | -------------- |
| Cray           | `man craycc` | `man crayCC` | `man crayftn`  |
| GNU            | `man gcc`    | `man g++`    | `man gfortran` |

!!! tip
    There are no `man` pages for the AOCC compilers at the moment.

## Linking on ARCHER2

Executables on ARCHER2 link dynamically, and the Cray Programming
Environment does not currently support static linking. This is in
contrast to ARCHER where the default was to build statically.

If you attempt to link statically, you will see errors similar to:

    /usr/bin/ld: cannot find -lpmi
    /usr/bin/ld: cannot find -lpmi2
    collect2: error: ld returned 1 exit status

The compiler wrapper scripts on ARCHER link runtime libraries in using
the `RUNPATH` by default. This means that the paths to the runtime
libraries are encoded into the executable so you do not need to load the
compiler environment in your job submission scripts.

### Using RPATHs to link

The default behaviour of a dynamically linked executable will be to
allow the linker to provide the libraries it needs at runtime by
searching the paths in the `LD_LIBRARY_PATH` environment variable. This
is flexible in that it allows an executable to use newly installed
library versions without rebuilding, but in some cases you may prefer to
bake the paths to specific libraries into the executable, keeping them
constant. While the libraries are still dynamically loaded at run time,
from the end user's point of view the resulting behaviour will be
similar to that of a statically compiled executable in that they will
not need to concern themselves with ensuring the linker will be able to
find the libraries.

This is achieved by providing RPATHs to the compiler as options. To set
the compiler wrappers to do this, you can set the following environment
variable:

    export CRAY_ADD_RPATH=yes

You can also provide RPATHs directly to the compilers using the
`-Wl,-rpath=<path-to-directory>` flag, where the provided path is to the
directory containing the libraries which are themselves typically
specified with flags of the type `-l<library-name>`.

## Debugging tools

The following debugging tools are available on ARCHER2:

  - **gdb4hpc** is a command-line tool working similarly to
    [gdb](https://www.gnu.org/software/gdb/) that allows users to debug
    parallel programs. It can launch parallel programs or attach to ones
    already running and allows the user to step through the execution to
    identify the causes of any unexpected behaviour. Available via
    `module load gdb4hpc`.
  - **valgrind4hpc** is a parallel memory debugging tool that aids in
    detection of memory leaks and errors in parallel applications. It
    aggregates like errors across processes and threads to simplify
    debugging of parallel appliciations. Available via `module load
    valgrind4hpc`.
  - **STAT**, the Stack Trace Analysis Tool, generates merged stack
    traces for parallel applications. It also provides visualisation
    tools. Available via `module load cray-stat`.

To get started debugging on ARCHER2, you might like to use gdb4hpc. You
should first of all compile your code using the `-g` flag to enable
debugging symbols. Once compiled, load the gdb4hpc module and start it:

    module load gdb4hpc
    gdb4hpc

Once inside gdb4hpc, you can start your program's execution with the
`launch` command:

    dbg all> launch $my_prog{128} ./prog

In this example, a job called `my_prog` will be launched to run the
executable file `prog` over 128 cores on a compute node. If you run
`squeue` in another terminal you will be able to see it running. Inside
gdb4hpc you may then `step` through the code's execution, `continue` to
breakpoints that you set with `break`, `print` the values of variables
at these points, and perform a `backtrace` on the stack if the program
crashes. Debugging jobs will end when you exit gdb4hpc, or you can end
them yourself by running, in this example, `release $my_prog`.

For more information on debugging parallel codes, see the documentation
in the [Debugging section](../user-guide/debug.md) of the ARCHER2 User and Best Practice Guide.

## Profiling tools

Profiling on ARCHER2 is provided through the Cray Performance
Measurement and Analysis Tools (CrayPAT). This has a number of different
components:

  - **CrayPAT** the full-featured program analysis tool set. CrayPAT
    consists of pat\_build, the utility used to instrument programs, the
    CrayPat run time environment, which collects the specified
    performance data during program execution, and pat\_report, the
    first-level data analysis tool, used to produce text reports or
    export data for more sophisticated analysis
  - **CrayPAT-lite** a simplified and easy-to-use version of CrayPAT
    that provides basic performance analysis information automatically,
    with a minimum of user interaction.
  - **Reveal** the next-generation integrated performance analysis and
    code optimization tool, which enables the user to correlate
    performance data captured during program execution directly to the
    original source, and identify opportunities for further
    optimization.
  - **Cray PAPI** components, which are support packages for those who
    want to access performance counters.
  - **Cray Apprentice2** the second-level data analysis tool, used to
    visualize, manipulate, explore, and compare sets of program
    performance data in a GUI environment.

The above tools are made available for use by firstly loading the
`perftools-base` module followed by either `perftools` (for CrayPAT,
Reveal and Apprentice2) or one of the `perftools-lite` modules.

The simplest way to get started profiling your code is with
CrayPAT-lite. For example, to sample a run of a code you would load the
`perftools-base` and `perftools-lite` modules, and then compile (you
will receive a message that the executable is being instrumented).
Performing a batch run as usual with this executable will produce a
directory such as `my_prog+74653-2s` which can be passed to `pat_report`
to view the results. In this example,

    pat_report -O calltree+src my_prog+74653-2s

will produce a report containing the call tree. You can view available
report keywords to be provided to the `-O` option by running `pat_report -O -h`.
The available `perftools-lite` modules are:

  - `perftools-lite`, instrumenting a basic sampling experiment.
  - `perftools-lite-events`, instrumenting a tracing experiment.
  - `perftools-lite-gpu`, instrumenting OpenACC and OpenMP 4 use of
    GPUs.
  - `perftools-lite-hbm`, instrumenting for memory bandwidth usage.
  - `perftools-lite-loops`, instrumenting a loop work estimate
    experiment.

!!! tip
    For more information on profiling parallel codes, see the documentation
    in the [Profiling section](../user-guide/profile.md) of the
    ARCHER2 User and Best Practice Guide.

## Useful Links

Links to other documentation you may find useful:

  - [ARCHER2 User and Best Practice Guide](../../user-guide/) -
    Covers all aspects of use of the ARCHER2 service. This includes
    fundamentals (required by all users to use the system effectively),
    best practice for getting the most out of ARCHER2, and more advanced
    technical topics.
  - [HPE Cray Programming Environment User
    Guide](https://internal.support.hpe.com/hpesc/public/docDisplay?docLocale=en_US&docId=a00115304en_us)
  - [HPE Cray Performance Measurement and Analysis Tools User
    Guide](https://support.hpe.com/hpesc/public/docDisplay?docLocale=en_US&docId=a00114942en_us)
