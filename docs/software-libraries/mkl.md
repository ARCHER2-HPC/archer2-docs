# Intel Math Kernel Library (MKL)

The Intel Maths Kernel Libraries (MKL) contain a variety of optimised numerical
libraries including BLAS, LAPACK, ScaLAPACK and FFTW. In general, the exact commands
required to build against MKL depend on the details of compiler, environment,
requirements for parallelism, and so on.
The [Intel MKL link line advisor](https://software.intel.com/content/www/us/en/develop/articles/intel-mkl-link-line-advisor.html) should be consulted.

Some examples are given below. Note that loading the `mkl` module will provide 
the environment variable `MKLROOT` which holds the location of the various MKL components.

!!! important
    The `cray-libsci` module is loaded by default for all users and this module 
    also contains definitions of BLAS, LAPACK and ScaLAPACK routines that conflict
    with those in MKL. The `mkl` module automatically unloads `cray-libsci`.

!!! important
    The `mkl` module needs to be loaded both at compile time and at runtime
    (usually in your job submission script).

!!! tip
    MKL only supports the GCC programming environment (`PrgEnv-gnu`).
    Other programming environments may work but this is untested and unsupported
    on ARCHER2.

!!! note
    Loading the `mkl/19.5-281` module sets the environment variable `MKL_DEBUG_CPU_TYPE=5`
    which is required to get good performance on AMD systems. More recent
    versions of MKL do not support this option.

## Serial MKL with GCC

Swap modules:

=== Full system ===
    ```
    module load PrgEnv-gnu
    module load mkl
    ```
=== 4-cabinet system ===
    ```
    module restore PrgEnv-gnu
    module load mkl
    ```

| Language | Compile options | Link options |
|----------|-|-|
| Fortran | `-m64  -I"${MKLROOT}/include"` | `-L${MKLROOT}/lib/intel64 -Wl,--no-as-needed -lmkl_gf_lp64 -lmkl_sequential -lmkl_core -lpthread -lm -ldl` |
| C/C++ | ` -m64  -I"${MKLROOT}/include"` | `-L${MKLROOT}/lib/intel64 -Wl,--no-as-needed -lmkl_intel_lp64 -lmkl_sequential -lmkl_core -lpthread -lm -ldl` |

## Threaded MKL with GCC

Swap modules:

=== Full system ===
    ```
    module load PrgEnv-gnu
    module load mkl
    ```
=== 4-cabinet system ===
    ```
    module restore PrgEnv-gnu
    module load mkl
    ```

| Language | Compile options | Link options |
|----------|-|-|
| Fortran | `-m64  -I"${MKLROOT}/include"` | `  -L${MKLROOT}/lib/intel64 -Wl,--no-as-needed -lmkl_gf_lp64 -lmkl_gnu_thread -lmkl_core -lgomp -lpthread -lm -ldl` |
| C/C++ | ` -m64  -I"${MKLROOT}/include"` | ` -L${MKLROOT}/lib/intel64 -Wl,--no-as-needed -lmkl_intel_lp64 -lmkl_gnu_thread -lmkl_core -lgomp -lpthread -lm -ldl` |

## MKL parallel ScaLAPACK with GCC

Swap modules:

=== Full system ===
    ```
    module load PrgEnv-gnu
    module load mkl
    ```
=== 4-cabinet system ===
    ```
    module restore PrgEnv-gnu
    module load mkl
    ```

| Language | Compile options | Link options |
|----------|-|-|
| Fortran | `-m64  -I"${MKLROOT}/include"` | `-L${MKLROOT}/lib/intel64 -lmkl_scalapack_lp64 -Wl,--no-as-needed -lmkl_gf_lp64 -lmkl_gnu_thread -lmkl_core -lmkl_blacs_intelmpi_lp64 -lgomp -lpthread -lm -ldl` |
| C/C++ | ` -m64  -I"${MKLROOT}/include"` | `-L${MKLROOT}/lib/intel64 -lmkl_scalapack_lp64 -Wl,--no-as-needed -lmkl_intel_lp64 -lmkl_gnu_thread -lmkl_core -lmkl_blacs_intelmpi_lp64 -lgomp -lpthread -lm -ldl` |
