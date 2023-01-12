# ARCHER2 Upgrade: 2023

!!! warning
    The information on this page represents our current best understanding of the
    upgrade but is subject to change and revision as more information becomes 
    available.
    
    Last updated: 2023-01-12

During the first half of 2023 ARCHER will go through a major software upgrade. This will
upgrade the versions of the following components on the service:

 - Base operating system
    + Compute nodes: Cray OS (COS) 2.4.103 based on SLES 15 SP4
    + Login nodes: UAN 2.5.7 based on SLES 15 SP4
 - Slingshot interconnect system software - to version 2.0.1
 - HPE Cray Programming Environment (PE) - to version 22.10
 - HPE Cray Management Software (CMS) - to version 1.3.1

On this page we describe the background to the changes, the current
information on the timeline, what impact the changes will have for users and
any action you should expect to have to take following the upgrade.

## Why is the upgrade happening?

 - Software is out of date
 - Less access to security updates
 - Improvements to upgradeability, maintainability and monitoring
 - Improvements to interconnect reliability and performance
 - General improvements

## When will the upgrade happen and how long will it take?

 - Start: TBC
 - Duration: TBC

## What are the impacts on users from the upgrade?

During the upgrade process:

 - No login access
 - No access to any data on the system

After the upgrade process:

 - Recompile and test code
 - No Python v2 available as part of supported software

## What software versions will be available after the upgrade?

System software:

 - Base operating system
    + Compute nodes: Cray OS (COS) 2.4.103 based on SLES 15 SP4
    + Login nodes: UAN 2.5.7 based on SLES 15 SP4
 - Slingshot interconnect system software: 2.0.1
 - HPE Cray Management Software (CMS): 1.3.1

### Programming environment: 22.10

Compilers:

 - CCE: 14.0.4
 - GCC: 12.1.0 (11.2.0, 10.3.0 also available)
 - AOCC: 3.2

Communication libraries:

 - Cray MPICH: 8.1.20 
    + Based on MPICH 3.4a2
    + Supports OpenFabrics (OFI) and UCX
  - Cray OpenSHMEMX: 11.5.6

Numerical libraries:

 - Cray LibSci: 22.10.1.2
 - FFTW: 3.3.10.2

IO Libraries:

 - HDF5: 1.12.2.1
 - NetCDF: 4.9.0.1
 - Parallel NetCDF: 1.12.3.1

Tools:

 - Python: 3.9.13
    + numpy: 1.21.5
    + scipy: 1.6.2
    + mpi4py: 3.1.3
    + dask: 2022.2.1
 - R: 4.2.1.1
 - CrayPAT/Perftools: 22.09.0
 - gdb4hpc: 4.14.4
 - valgrind4hpc: 2.12.10
 - sanitizers4hpc: 1.0.2
 - Lmod: 3.1.4

#### Summary of user and application impact

For full information, see [CPE 22.10 Release Notes](https://github.com/PE-Cray/cpe-changelog/blob/main/ex/cpe-22.10-sles15-sp3-FullReleaseNotes.txt)

**CCE 14**

C++ applications built using CCE 13 or earlier may need to be recompiled due to the significant
changes that were necessary to implement C++17.  This is expected to be a one-time requirement.

Some non-standard Cray Fortran extensions supporting shorthand notation for logical operations
will be removed in a future release.  CCE 14 will issue warning messages when these are
encountered, providing time to adapt the application to use standard Fortran.  

**HPE Cray MPICH 8.1.20**

Cray MPICH 8.1.20 can support only ~2040 simultaneous MPI communicators.

