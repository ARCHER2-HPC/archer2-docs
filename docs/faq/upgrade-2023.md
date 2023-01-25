# ARCHER2 Upgrade: 2023

!!! warning
    The information on this page represents our current best understanding of the
    upgrade but is subject to change and revision as more information becomes 
    available.
    
    Last updated: 2023-01-24

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

If you have any questions or concerns regarding the upgrade, please
[contact the ARCHER2 Service Desk](https://www.archer2.ac.uk/support-access/servicedesk.html).

## Why is the upgrade happening?

There are a number of reasons why ARCHER2 needs to go through this major software
upgrade. All of these reasons are related to the fact that the current system
software setup is out of date; due to this, maintenance of the service is very
difficult and updating software within the current framework is not possible.
Some specific issues are:

 - *Ongoing access to security updates* - due to the out-of-date nature of the system
   software on ARCHER2, the vendor (HPE) can not provide security updates for the current
   system software beyond the upgrade date.
 - *Improvements to interconnect reliability and performance* - the Slingshot interconnect
   that links compute nodes together and to the high-performance Lustre file systems
   is currently running an old version of management software that has a number of known
   limitations and bugs. These affect our ability to monitor the health of the interconnect
   and have led to reliability issues for calculations using large numbers of compute nodes.
   Without a major system software upgrade, we cannot move to an up to date version of the
   Slingshot software that addresses current limitations.
 - *Improvements to upgradeability, maintainability and monitoring* - the current system software
   is based on a collection of early versions from the hardware vendor that do not provide
   the system health monitoring characteristics of more recent versions or the ability to
   flexibly update the system with low impact on user service that are available in more 
   recent versions.
 - *Access to more recent compilers, software libraries and tools* - the current system
   software does not provide the ability to access improvements in compilers and libraries
   (both bug fixes and performance improvements) that are available from HPE. Once the 
   system software is updated, the service will be able to access this improved software.

## When will the upgrade happen and how long will it take?

This major software upgrade will involve a complete re-install of system software followed
by a reinstatement of local configurations (e.g. Slurm, authentication services, SAFE integration)
and so will require an extended period of downtime.

 - Start: TBC
 - Duration: TBC

## What are the impacts on users from the upgrade?

During the upgrade process:

 - No login access
 - No access to any data on the system

After the upgrade process:

 - Recompile and test code - as the new system will be based on a new OS version and
   new versions of compilers and libraries we strongly recommend that all users recompile
   and test all software on the service. The ARCHER2 CSE service will be recompiling all 
   centrally installed software.
 - Note: there will be no Python 2 installation available as part of supported software
   following the upgrade. Python 3 will continue to be fully-supported.

Impact on allocations:

 - TBC

Impact on data on the service

 - No data in /home, /work, NVMe or RDFaaS will be removed or moved as part of the upgrade

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

