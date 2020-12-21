# Main differences between ARCHER and ARCHER2

This section provides an overview of the main differences between
ARCHER and ARCHER2 along with links to more information where 
appropriate.

## For all users

  - You use the [new SAFE](https://safe.epcc.ed.ac.uk) rather than the ARCHER SAFE
  - You can add multiple SSH keys to your ARCHER2 account using SAFE
  - There are 128 cores on an ARCHER2 compute node rather than 24
  - ARCHER2 usage is charged in CUs (Compute Units) rather than kAU
    - Generally: 1.5156 kAU = 4.21 ARCHER node hour = 1 ARCHER2 node hour = 1 CU
  - ARCHER2 uses the *Slurm* scheduler instead of PBS Pro
    - See: [Running jobs on ARCHER2](../user-guide/scheduler.md)
  - Parallel applications are launched using `srun` rather than `aprun`
  - You cannot currently query your budget on ARCHER2 itself, you can 
    view your budget using SAFE

## For users compiling and developing software on ARCHER2

  - The Intel compilers are not available on ARCHER2
    - ARCHER2 supports the Cray, Gnu and AMD compilers
    - See: [Application development environment](../user-guide/dev-environment.md)
  - Intel MKL libraries are not available on ARCHER2
    - Use Cray LibSci for BLAS/LAPACK/ScaLAPACK
    - Use FFTW for FFTs
  - All binaries on ARCHER2 are dynamically linked
    - Static linking is currently not possible on ARCHER2
    - This means that all binaries must be installed on the /work file systems
      as these are the only file systems available on the compute nodes