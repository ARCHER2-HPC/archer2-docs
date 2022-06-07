# FHI-aims

[FHI-aims](https://fhi-aims.org) is an all-electron electronic structure code based on numeric
atom-centered orbitals. It enables first-principles simulations with
very high numerical accuracy for production calculations, with excellent
scalability up to very large system sizes (thousands of atoms) and up to
very large, massively parallel supercomputers (ten thousand CPU cores).

## Useful Links

   - [FHI-aims website](https://fhi-aims.org)
   - [FHI-aims Tutorials](https://fhi-aims-club.gitlab.io/tutorials/tutorials-overview/)

## Using FHI-aims on ARCHER2

**FHI-aims is only available to users who have a valid FHI-aims licence.**

If you have a FHI-aims licence and wish to have access to FHI-aims on
ARCHER2, please make a request via the SAFE, see:

   - [How to request access to package
     groups](https://epcced.github.io/safe-docs/safe-for-users/#how-to-request-access-to-a-package-group)

Please have your license details to hand.

## Running parallel FHI-aims jobs

The following script will run a FHI-aims job using 8 nodes (1024 cores). The script
assumes that the input have the default names `control.in` and `geometry.in`.

=== "Full system"
    ```
    #!/bin/bash
    
    # Request 2 nodes with 128 MPI tasks per node for 20 minutes
    #SBATCH --job-name=FHI-aims
    #SBATCH --nodes=8
    #SBATCH --tasks-per-node=128
    #SBATCH --cpus-per-task=1
    #SBATCH --time=00:20:00
    
    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard
    
    # Load the FHI-aims module, avoid any unintentional OpenMP threading by
    # setting OMP_NUM_THREADS, and launch the code.
    module load fhiaims

    export OMP_NUM_THREADS=1
    srun --distribution=block:block --hint=nomultithread aims.mpi.x
    ```

## Compiling FHI-aims

The latest instructions for building FHI-aims on ARCHER2 may be found in
the GitHub repository of build instructions:

   - [Build instructions for FHI-aims on
     GitHub](https://github.com/hpc-uk/build-instructions/tree/main/apps/FHI-aims/ARCHER2-210716_2)
