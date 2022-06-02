# ONETEP

ONETEP (Order-N Electronic Total Energy Package) is a linear-scaling
code for quantum-mechanical calculations based on density-functional
theory.

## Useful Links

  - [ONETEP home page](https://www.onetep.org)
  - [ONETEP tutorials](https://www.onetep.org/Main/Tutorials)
  - [ONETEP documentation](https://www.onetep.org/Main/Documentation)

## Using ONETEP on ARCHER2

**ONETEP is only available to users who have a valid ONETEP licence.**

If you have a ONETEP licence and wish to have access to ONETEP on
ARCHER2, please make a request via the SAFE, see:

   - [How to request access to package
     groups](https://epcced.github.io/safe-docs/safe-for-users/#how-to-request-access-to-a-package-group)

Please have your license details to hand.

## Running parallel ONETEP jobs

The following script will run a ONETEP job using 2 nodes (256 cores) with
16 MPI processes per node and 8 OpenMP threads per MPI process. It
assumes that the input file is called `text_calc.dat`.

=== "Full system"
    ```
    #!/bin/bash

    # Request 2 nodes for 20 minutes
    #    - 16 MPI processes per node (32 in total)
    #    - 8 OpenMP threads per MPI process
    #    - 256 cores used in total

    #SBATCH --job-name=ONETEP
    #SBATCH --nodes=2
    #SBATCH --tasks-per-node=16
    #SBATCH --cpus-per-task=8
    #SBATCH --time=00:20:00

    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code] 
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Load the ONETEP module - use 6.1.9.0 to avoid issues on ARCHER2
    # The module also sets:
    #    - export OMP_PLACES=cores
    #    - export OMP_PROC_BIND=close
    #    - export FI_MR_CACHE_MAX_COUNT=0
    module load onetep/6.1.9.0-CCE-LibSci

    # Set the threading (usually the same as --cpus-per-task)
    export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK

    # Launch the executable
    srun --distribution=block:block --hint=nomultithread onetep.archer2 test_calc > test_calc.out
    ```

## Hints and Tips

See the information in the [ONETEP documentation](https://www.onetep.org/Main/RunningONETEP).

## Compiling ONETEP

The latest instructions for building ONETEP on ARCHER2 may be found in
the GitHub repository of build instructions:

   - [Build instructions for ONETEP on
     GitHub](https://github.com/hpc-uk/build-instructions/tree/main/apps/ONETEP)
