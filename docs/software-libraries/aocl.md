# AOCL

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

## Compiling with AOCL

!!! note
    Currently AOCL is only available with the GNU Programming Environment.

```
module load PrgEnv-gnu
module load aocl
```

When loaded, this collection of numerical libraries replaces ```cray-libsci``` and ```cray-fftw```.

!!! warning
    When using ```gcc/11.2.0```, the LibM library is unavailable. This is a known issue that is being investigated.  

# Resources  

For more information on AOCL, please see: https://developer.amd.com/amd-aocl/#documentation 

# Version history..
- Module `aocl/3.1` installed April 2022
