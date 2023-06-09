# AMD Optimizing CPU Libraries (AOCL)

AMD Optimizing CPU Libraries (AOCL) are a set of numerical libraries optimized for AMD “Zen”-based processors, including EPYC, Ryzen Threadripper PRO, and Ryzen.

AOCL is comprised of eight libraries:
- BLIS (BLAS Library)
- libFLAME (LAPACK)
- AMD-FFTW
- LibM (AMD Core Math Library)
- ScaLAPACK
- AMD Random Number Generator (RNG)
- AMD Secure RNG
- AOCL-Sparse

!!! tip
    AOCL `3.1` and `4.0` are available. `3.1` is default. 


## Compiling with AOCL

!!! important
    AOCL does not currently support the Cray programming environment and is
    currently unavailable with `PrgEnv-cray` loaded.

!!! important
    The `cray-libsci` module is loaded by default for all users and this module
    also contains definitions of BLAS, LAPACK and ScaLAPACK routines that conflict
    with those in AOCL. The `aocl` module automatically unloads `cray-libsci`.


### GNU Programming Environment

AOCL `3.1` and `4.0` is available for all versions of the GCC compilers: `gcc/11.2.0` and `gcc/10.3.0`

```
module load PrgEnv-gnu
module load aocl
```

### AOCC Programming Environment

AOCL `3.1` and `4.0` is available for all versions of the AOCC compilers: `aocc/3.2.0`.

```
module load PrgEnv-aocc
module load aocl
```


## Resources  

For more information on AOCL, please see: https://developer.amd.com/amd-aocl/#documentation

## Version history

Current modules:

- `aocl/3.1` installed June 2023
- `aocl/4.0` installed June 2023
