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
Three executable versions are available on the normal (CPU-only) modules:

  - Parallel MPI/OpenMP, single precision: `gmx_mpi`
  - Parallel MPI/OpenMP, double precision: `gmx_mpi_d`
  - Serial, single precision: `gmx`

We also provide a GPU version of GROMACS that will run on the MI210 GPU nodes, it's named `gromacs/2022.4-GPU` and can be loaded with

```bash
module load gromacs/2022.4-GPU
```

!!! important
    The `gromacs` modules reset the CPU frequency to the highest possible value
    (2.25 GHz) as this generally achieves the best balance of performance to 
    energy use. You can change this setting by following the instructions in the
    [Energy use section](../user-guide/energy.md) of the User Guide.

## Running parallel GROMACS jobs

### Running MPI only jobs

The following script will run a GROMACS MD job using 4 nodes (128x4 cores) with pure MPI.

```slurm
#!/bin/bash

#SBATCH --job-name=mdrun_test
#SBATCH --nodes=4
#SBATCH --ntasks-per-node=128
#SBATCH --cpus-per-task=1
#SBATCH --time=00:20:00

# Replace [budget code] below with your project code (e.g. t01)
#SBATCH --account=[budget code]
#SBATCH --partition=standard
#SBATCH --qos=standard

# Setup the environment
module load gromacs

# Ensure the cpus-per-task option is propagated to srun commands
export SRUN_CPUS_PER_TASK=$SLURM_CPUS_PER_TASK

export OMP_NUM_THREADS=1 
srun --distribution=block:block --hint=nomultithread gmx_mpi mdrun -s test_calc.tpr
```

### Running hybrid MPI/OpenMP jobs

The following script will run a GROMACS MD job using 4 nodes (128x4
cores) with 6 MPI processes per node (24 MPI processes in total) and 6
OpenMP threads per MPI process.

```slurm
#!/bin/bash
#SBATCH --job-name=mdrun_test
#SBATCH --nodes=4
#SBATCH --ntasks-per-node=16
#SBATCH --cpus-per-task=8
#SBATCH --time=00:20:00

# Replace [budget code] below with your project code (e.g. t01)
#SBATCH --account=[budget code]
#SBATCH --partition=standard
#SBATCH --qos=standard

# Setup the environment
module load gromacs

# Ensure the cpus-per-task option is propagated to srun commands
export SRUN_CPUS_PER_TASK=$SLURM_CPUS_PER_TASK

export OMP_NUM_THREADS=8
srun --distribution=block:block --hint=nomultithread gmx_mpi mdrun -s test_calc.tpr
```

### Running GROMACS on the AMD MI210 GPUs

The following script will run a GROMACS MD job using 1 GPU with 1 MPI process 8 OpenMP threads per MPI process.

```slurm
#!/bin/bash
#SBATCH --job-name=mdrun_gpu
#SBATCH --gpus=1
#SBATCH --time=00:20:00
#SBATCH --hint=nomultithread
#SBATCH --distribution=block:block

# Replace [budget code] below with your project code (e.g. t01)
#SBATCH --account=[budget code]
#SBATCH --partition=gpu
#SBATCH --qos=gpu-shd  # or gpu-exc

# Setup the environment
module load gromacs/2022.4-GPU

export OMP_NUM_THREADS=8
srun --ntasks=1 --cpus-per-task=8 gmx_mpi mdrun -ntomp 8 --noconfout -s calc.tpr
```

## Compiling Gromacs

The latest instructions for building GROMACS on ARCHER2 may be found in
the GitHub repository of build instructions:

   - [Build instructions for GROMACS on
     GitHub](https://github.com/hpc-uk/build-instructions/tree/main/apps/GROMACS)
