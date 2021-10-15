# NAMD

[NAMD](http://www.ks.uiuc.edu/Research/namd/), recipient of a 2002
Gordon Bell Award and a 2012 Sidney Fernbach Award, is a parallel
molecular dynamics code designed for high-performance simulation of
large biomolecular systems. Based on Charm++ parallel objects, NAMD
scales to hundreds of cores for typical simulations and beyond 500,000
cores for the largest simulations. NAMD uses the popular molecular
graphics program VMD for simulation setup and trajectory analysis, but
is also file-compatible with AMBER, CHARMM, and X-PLOR.

## Useful Links

  - [NAMD User Guide](http://www.ks.uiuc.edu/Research/namd/2.13/ug/)
  - [NAMD Tutorials](https://www.ks.uiuc.edu/Training/Tutorials/#namd)

## Using NAMD on ARCHER2

NAMD is freely available to all ARCHER2 users.

## Running parallel NAMD jobs

!!! note
    The simplest approach, and the one that is likely to give the
    best performance, is to use the pure MPI version of NAMD
    (i.e. _nosmp_ mode) which involves loading a non-default NAMD
    module `namd/2.14-nosmp-gcc10`.

The following script will run a pure MPI NAMD MD job using 4 nodes (i.e.
128x4 = 512 CPU-cores).

```
#!/bin/bash

# Request four nodes to run a job of 512 MPI tasks with 128 MPI
# tasks per node, here for maximum time 20 minutes.

#SBATCH --job-name=namd_nosmp
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

module load namd/2.14-nosmp-gcc10

srun --distribution=block:block --hint=nomultithread namd2 input.namd
```

If your jobs runs out of memory, then you can can run the _smp_
version of NAMD which uses less memory. This involves launching a
combination of MPI processes for communication and worker threads
which perform computation.

!!! note
    The optimal choice for the numbers of MPI processes and worker
    threads depends on the dataset and the number of compute
    nodes. Before running large production jobs, it is worth
    experimenting with these parameters to find the optimal
    configuration for your particular simulation.

The following script will run an SMP NAMD MD job using 4 nodes (i.e.
128x4 = 512 CPU-cores) with 32 MPI communication processes per node
and 96 worker threads. The number of workers per communication process
is specified by the `+ppn` argument to NAMD, which is set here to
`cpus-per-task - 1`, i.e `+ppn 3`, to leave a CPU-core free for the
associated MPI process.

```
#!/bin/bash
#SBATCH --job-name=namd-smp
#SBATCH --tasks-per-node=32
#SBATCH --cpus-per-task=4
#SBATCH --nodes=4
#SBATCH --time=00:20:00
# Replace [budget code] below with your project code (e.g. t01)
#SBATCH --account=[budget code] 
#SBATCH --partition=standard
#SBATCH --qos=standard

# Load the relevant modules
module load epcc-job-env
module load namd

# Set procs per node (PPN) & OMP_NUM_THREADS
export PPN=$(($SLURM_CPUS_PER_TASK-1))
export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK
export OMP_PLACES=cores

# Record PPN in the output file
echo "Number of worker threads PPN = $PPN"

# Run NAMD
srun --distribution=block:block --hint=nomultithread namd2 +setcpuaffinity +ppn $PPN input.namd
```

This is likely to be a reasonable first choice but you should
experiment with the values of `tasks-per-node` and `cpus-per-task`. To
use all the CPU-cores in a node, the product of these two values
should be 128.

If you cannot run the pure MPI version of NAMD, then the optimal
values of (`tasks-per-node`, `cpus-per-task`) are likely to be either
(32,4), (16,8) or (8,16).

## Compiling NAMD

The latest instructions for building NAMD on ARCHER2 may be found in
the GitHub repository of build instructions:

   - [Build instructions for CASTEP on
     GitHub](https://github.com/hpc-uk/build-instructions/tree/main/apps/NAMD)
