# ARCHER2 Upgrade: 2023

During the first half of 2023 ARCHER went through a major software upgrade.

On this page we describe the background to the changes what impact the changes have had for users, any
action you should expect to take following the upgrade and information
on the versions on updated software.

If you have any questions or concerns, please
[contact the ARCHER2 Service Desk](https://www.archer2.ac.uk/support-access/servicedesk.html).

## Why did the upgrade happen?

There are a number of reasons why ARCHER2 needed to go through this major software
upgrade. All of these reasons are related to the fact that the previous system
software setup was out of date; due to this, maintenance of the service was very
difficult and updating software within the current framework was not possible.
Some specific issues were:

 - *Ongoing access to security updates* -- the vendor (HPE) could not provide support for
   security updates for the previous system software on ARCHER2 indefinitely, this would have
   been an issue if we had continued with the previous release.
 - *Improvements to interconnect reliability and performance* -- the Slingshot interconnect
   that links compute nodes together and to the high-performance Lustre file systems
   was running an old version of management software that had a number of known
   limitations and bugs. These affected our ability to monitor the health of the interconnect
   and led to reliability issues for calculations using large numbers of compute nodes.
   Without the major system software upgrade, we could not move to an up to date version of the
   Slingshot software that addressed these limitations.
 - *Improvements to upgradeability, maintainability and monitoring* -- the previous system software
   was based on a collection of early versions from the hardware vendor that did not provide
   the system health monitoring characteristics of more recent versions or the ability to
   flexibly update the system with low impact on user service that were available in more 
   recent versions.
 - *Access to more recent compilers, software libraries and tools* -- the previous system
   software did not provide the ability to access improvements in compilers and libraries
   (both bug fixes and performance improvements) that were available from HPE.

## When did the upgrade happen and how long did it take?

This major software upgrade involved a complete re-install of system software followed
by a reinstatement of local configurations (e.g. Slurm, authentication services, SAFE integration).
Unfortunately, this major work required a long period of downtime but this was planned
with all service partners to minimise the outage and give as much notice to users as possible so
that they could plan accordingly.

The outage dates were:

 - Start: 14:00 BST, Fri 19th May 2023
 - End: 12:00 BST, Mon 12th June 2023

## What are the impacts on users from the upgrade?

### During the upgrade process

 - No login access
 - No access to any data on the system
 - Some of the SAFE functionality will be removed during the upgrade such as user account requests

### After the upgrade process

The allocation periods (where appropriate) were extended for the outage period. The changes were in place when the service was returned.

After the upgrade process there are a number of changes that may require action from users

#### Updated login node host keys

If you previously logged into the ARCHER2 system before the upgrade you may see an error from SSH that looks like:

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@       WARNING: POSSIBLE DNS SPOOFING DETECTED!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
The ECDSA host key for login.archer2.ac.uk has changed,
and the key for the corresponding IP address 193.62.216.43
has a different value. This could either mean that
DNS SPOOFING is happening or the IP address for the host
and its host key have changed at the same time.
Offending key for IP in /Users/auser/.ssh/known_hosts:11
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:UGS+LA8I46LqnD58WiWNlaUFY3uD1WFr+V8RCG09fUg.
Please contact your system administrator.
```

If you see this, you should delete the offending host key from your ~/.ssh/known_hosts file
(in the example above the offending line is line #11).

The current login node host keys are always [documented in the User Guide](../user-guide/connecting.md#host-keys)

#### Recompile and test software

As the new system is based on a new OS version and new versions of compilers and
libraries we strongly recommend that all users recompile and test all software on the service.
The ARCHER2 CSE service recompiled all centrally installed software.

#### No Python 2 installation

There is no Python 2 installation available as part of supported software
following the upgrade. Python 3 continues to be fully-supported.

### Impact on data on the service

 - No data in /home, /work, NVMe or RDFaaS was removed or moved as part of the upgrade

#### Slurm: cpus-per-task setting no longer inherited by `srun`

Change in Slurm behaviour. The setting from the `--cpus-per-task` option to sbatch/salloc is
no longer propagated by default to `srun` commands in the job script.

This can lead to very poor performance due to oversubscription of cores with processes/threads
if job submission scripts are not updated. The simplest workaround is to add the command:

```bash
export SRUN_CPUS_PER_TASK=$SLURM_CPUS_PER_TASK
```

before any srun commands in the script. You can also explicitly use the `--cpus-per-task` option
to srun if you prefer.

#### Change of Slurm "socket" definition

This change only affects users who use a placement scheme where placement of processes on
sockets is cyclic (e.g. `--distribution=block:cyclic`). The Slurm definition of a “socket”
has changed. The previous setting on ARCHER2 was that a socket = 16 cores (all share a DRAM memory
controller). On the updated ARCHER2, the setting of a socket = 4 cores (corresponding to a CCX -
Core CompleX). Each CCX shares 16 MB L3 Cache.

#### Changes to bind paths and library paths for Singularity with MPI

The paths you need to bind and the `LD_LIBRARY_PATH` settings required to use Cray MPICH with MPI 
in Singularity containers have changed. The updated settings are documented in the
[Containers section](../user-guide/containers.md) of the User and Best Practice Guide. This
also includes updated information on building containers with MPI to use on ARCHER2.

#### AMD &mu;Prof not available

The AMD &mu;Prof tool is not available on the upgraded system yet. We are working to get this 
fixed as soon as possible.

## What software versions will be available after the upgrade?

System software:

 - Base operating system
    + Compute nodes: Cray OS (COS) 2.4.109 based on SLES 15 SP4
    + Login nodes: UAN 2.5.8 based on SLES 15 SP4
 - Slingshot interconnect system software: 2.0.2
 - HPE Cray Management Software (CMS): 1.3.1
 - ARCHER2 CSE supported software

### Programming environment: 22.12

Compilers:

 - CCE: 15.0.0
 - GCC: 11.2.0 (10.3.0 also available)
 - AOCC: 3.2

Communication libraries:

 - Cray MPICH: 8.1.23 
    + Based on MPICH 3.4a2
    + Supports OpenFabrics (OFI) and UCX
  - Cray OpenSHMEMX: 11.5.7

Numerical libraries:

 - Cray LibSci: 22.12.1.1
 - FFTW: 3.3.10.3

IO Libraries:

 - HDF5: 1.12.2.1
 - NetCDF: 4.9.0.1
 - Parallel NetCDF: 1.12.3.1

Tools:

 - Python: 3.9.13.1
    + numpy: 1.21.5
    + scipy: 1.6.2
    + mpi4py: 3.1.3
    + dask: 2022.2.1
 - R: 4.2.1.1
 - CrayPAT/Perftools: 22.12.0
 - gdb4hpc: 4.14.6
 - valgrind4hpc: 2.12.10
 - sanitizers4hpc: 1.0.4
 - Lmod: 3.1.4

#### Summary of user and application impact of PE software

For full information, see [CPE 22.12 Release Notes](https://github.com/PE-Cray/cpe-changelog/blob/main/ex/cpe-22.12-sles15-sp4-FullReleaseNotes.txt)

**CCE 15**

C++ applications built using CCE 13 or earlier should be recompiled due to the significant
changes that were necessary to implement C++17.  This is expected to be a one-time requirement.

Some non-standard Cray Fortran extensions supporting shorthand notation for logical operations
will be removed in a future release.  CCE 15 will issue warning messages when these are
encountered, providing time to adapt the application to use standard Fortran.  

**HPE Cray MPICH 8.1.23**

Cray MPICH 8.1.23 can support only ~2040 simultaneous MPI communicators.

### CSE supported software

Default version in italics

|   Software   |   Versions   |
| --- | --- |
|   CASTEP   |   22.11, *23.11*   |
|   Code\_Saturne   |   7.0.1   |
|   ChemShell/PyChemShell   |   3.7.1/21.0.3   |
|   CP2K   |   2023.1   |
|   FHI-aims   |   221103   |
|   GROMACS   |   2022.4   |
|   LAMMPS   |   17\_FEB\_2023   |
|   NAMD   |   2.14   |
|   Nektar++   |   5.2.0   |
|   NWChem   |   7.0.2  |
|   ONETEP   |   6.9.1.0   |
|   OpenFOAM   |   v10.20230119 (.org), v2212 (.com)   |
|   Quantum Espresso   |   *6.8*, 7.1   |
|   VASP   |   5.4.4.pl2, 6.3.2, 6.4.1-vtst, *6.4.1*   |

|   Software   |   Versions   |
| --- | --- |
|   AOCL   |   3.1, *4.0*   |
|   Boost   |   1.81.0   |
|   GSL   |   2.7   |
|   HYPRE   |   2.18.0, *2.25.0*    |
|   METIS/ParMETIS   |   5.1.0/4.0.3   |
|   MUMPS   |   5.3.5, *5.5.1*   |
|   PETSc   |   13.14.2, *13.18.5*   |
|   PT/Scotch   |   6.1.0, *07.0.3*   |
|   SLEPC   |   13.14.1, *13.18.3*   |
|   SuperLU/SuperLU\_Dist   |   5.2.2 / 6.4.0, *8.1.2*   |
|   Trilinos   |   12.18.1   |



