# AMD Optimizing CPU Libraries (AOCL)

AMD Optimizing CPU Libraries (AOCL) are a set of numerical libraries optimized for AMD “Zen”-based processors, including EPYC, Ryzen Threadripper PRO, and Ryzen.

AOCL is comprised of the following eight libraries:
- BLIS (BLAS Library)
- libFLAME (LAPACK)
- AMD-FFTW
- LibM (AMD Core Math Library)
- ScaLAPACK
- AMD Random Number Generator (RNG)
- AMD Secure RNG
- AOCL-Sparse

!!! important
    The `cray-libsci` module is loaded by default for all users and this module
    also contains definitions of BLAS, LAPACK and ScaLAPACK routines that conflict
    with those in AOCL. The `aocl` module automatically unloads `cray-libsci`.

## Compiling with AOCL

!!! tip
    AOCL is currently unavailable for the Cray and AOCC programming environments (`PrgEnv-cray`, `PrgEnv-aocc`).
    This is untested and currently unsupported on ARCHER2.

```
module load aocl
```

# Resources  

For more information on AOCL, please see: https://developer.amd.com/amd-aocl/#documentation

# Version history..

- Module `aocl/3.1` installed April 2022
