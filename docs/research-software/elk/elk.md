# ELK

ELK is an all-electron full-potential linearised augmented-plane wave
(FP-LAPW) code with many advanced features. It was written originally at
Karl-Franzens-Universitt Graz as a milestone of the EXCITING EU Research
and Training Network.

## Useful Links

  - ELK home page <http://elk.sourceforge.net>
  - ELK documentation <http://elk.sourceforge.net/#documentation>

## Using ELK on ARCHER2

ELK is freely available to all users on ARCHER2.

## Running parallel ELK jobs

### Example MPI ELK job

The following script will run an ELK job on 4 nodes (512 cores).

=== "Full system"
    ```
    #!/bin/bash

    # Request 512 MPI tasks (4 nodes at 128 tasks per node) with a

    #SBATCH --job-name=elk_job
    #SBATCH --nodes=4
    #SBATCH --tasks-per-node=128
    #SBATCH --cpus-per-task=1
    #SBATCH --time=00:20:00

    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Load the relevant elk module
    module load elk

    srun --distribution=block:block --hint=nomultithread elk 
    ```

=== "4-cabinet system"
    ```
    #!/bin/bash

    # Request 512 MPI tasks (4 nodes at 128 tasks per node) with a

    #SBATCH --job-name=elk_job
    #SBATCH --nodes=4
    #SBATCH --tasks-per-node=128
    #SBATCH --cpus-per-task=1
    #SBATCH --time=00:20:00

    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Setup the job environment (this module needs to be loaded before any other modules)
    module load epcc-job-env
    # Load the relevant elk module
    module load elk

    srun --distribution=block:block --hint=nomultithread elk 
    ```


### Example mixed MPI/OpenMP ELK job

The following script will run an ELK job on 4 nodes, using 8 OpenMP
threads and 16 MPI tasks per node.

=== "Full system"
    ```
    #!/bin/bash

    # Request 4 nodes (using 8 threads and 16 MPI tasks per node) with a
    # maximum wall clock time limit of 20 minutes.
    # Replace [budget code] with your account code.

    #SBATCH --job-name=elk_job
    #SBATCH --nodes=4
    #SBATCH --tasks-per-node=16
    #SBATCH --cpus-per-task=8
    #SBATCH --time=00:20:00

    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Ensure OMP_NUM_THREADS is consistent with cpus-per-task above
    export OMP_NUM_THREADS=8

    # Load the relevant elk module
    module load elk

    srun --distribution=block:block --hint=nomultithread elk 
    ```

=== "4-cabinet system"
    ```
    #!/bin/bash

    # Request 4 nodes (using 8 threads and 16 MPI tasks per node) with a
    # maximum wall clock time limit of 20 minutes.
    # Replace [budget code] with your account code.

    #SBATCH --job-name=elk_job
    #SBATCH --nodes=4
    #SBATCH --tasks-per-node=16
    #SBATCH --cpus-per-task=8
    #SBATCH --time=00:20:00

    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Ensure OMP_NUM_THREADS is consistent with cpus-per-task above
    export OMP_NUM_THREADS=8

    # Setup the job environment (this module needs to be loaded before any other modules)
    module load epcc-job-env
    # Load the relevant elk module
    module load elk

    srun --distribution=block:block --hint=nomultithread elk 
    ```


## Compiling ELK

The latest instructions for building ELK on ARCHER2 may be found in
the GitHub repository of build instructions:

   - [Build instructions for ELK on
     GitHub](https://github.com/hpc-uk/build-instructions/tree/main/apps/ELK)
