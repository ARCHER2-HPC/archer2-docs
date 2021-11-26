# GROMACS

[GROMACS](http://www.gromacs.org/) is a versatile package to perform
molecular dynamics, i.e. simulate the Newtonian equations of motion for
systems with hundreds to millions of particles. It is primarily designed
for biochemical molecules like proteins, lipids and nucleic acids that
have a lot of complicated bonded interactions, but since GROMACS is
extremely fast at calculating the nonbonded interactions (that usually
dominate simulations) many groups are also using it for research on
non-biological systems, e.g. polymers.

## Useful Links

  - [GROMACS User Guides](http://manual.gromacs.org/documentation/)
  - [GROMACS Tutorials](http://www.gromacs.org/Documentation/Tutorials)

## Using GROMACS on ARCHER2

GROMACS is Open Source software and is freely available to all users.
Three versions are available:

  - Parallel MPI/OpenMP, single precision: `gmx_mpi`
  - Parallel MPI/OpenMP, double precision: `gmx_mpi_d`
  - Serial, single precision: `gmx`

## Running parallel GROMACS jobs

### Running MPI only jobs

The following script will run a GROMACS MD job using 4 nodes (128x4
cores) with pure MPI.

=== "Full system"
    ```
    #!/bin/bash

    #SBATCH --job-name=mdrun_test
    #SBATCH --nodes=4
    #SBATCH --tasks-per-node=128
    #SBATCH --cpus-per-task=1
    #SBATCH --time=00:20:00

    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Setup the environment
    module load gromacs

    export OMP_NUM_THREADS=1 
    srun --distribution=block:block --hint=nomultithread gmx_mpi mdrun -s test_calc.tpr
    ```
=== "4-cabinet system"
    ```
    #!/bin/bash

    #SBATCH --job-name=mdrun_test
    #SBATCH --nodes=4
    #SBATCH --tasks-per-node=128
    #SBATCH --cpus-per-task=1
    #SBATCH --time=00:20:00

    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Setup the environment
    module load epcc-job-env
    module load gromacs

    export OMP_NUM_THREADS=1 
    srun --distribution=block:block --hint=nomultithread gmx_mpi mdrun -s test_calc.tpr
    ```

### Running hybrid MPI/OpenMP jobs

The following script will run a GROMACS MD job using 4 nodes (128x4
cores) with 6 MPI processes per node (24 MPI processes in total) and 6
OpenMP threads per MPI process.

=== "Full system"
    ```
    #!/bin/bash
    #SBATCH --job-name=mdrun_test
    #SBATCH --nodes=4
    #SBATCH --tasks-per-node=16
    #SBATCH --cpus-per-task=8
    #SBATCH --time=00:20:00

    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Setup the environment
    module load gromacs

    export OMP_NUM_THREADS=8
    srun --distribution=block:block --hint=nomultithread gmx_mpi mdrun -s test_calc.tpr
    ```
=== "4-cabinet system"
    ```
    #!/bin/bash
    #SBATCH --job-name=mdrun_test
    #SBATCH --nodes=4
    #SBATCH --tasks-per-node=16
    #SBATCH --cpus-per-task=8
    #SBATCH --time=00:20:00

    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Setup the environment
    module load epcc-job-env
    module load gromacs

    export OMP_NUM_THREADS=8
    srun --distribution=block:block --hint=nomultithread gmx_mpi mdrun -s test_calc.tpr
    ```

## Compiling Gromacs

The latest instructions for building GROMACS on ARCHER2 may be found in
the GitHub repository of build instructions:

   - [Build instructions for GROMACS on
     GitHub](https://github.com/hpc-uk/build-instructions/tree/main/apps/GROMACS)
