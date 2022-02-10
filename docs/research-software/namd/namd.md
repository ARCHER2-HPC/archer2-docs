# NAMD

[NAMD](http://www.ks.uiuc.edu/Research/namd/) is an award-winning
parallel molecular dynamics code designed for high-performance simulation
of large biomolecular systems. Based on Charm++ parallel objects, NAMD
scales to hundreds of cores for typical simulations and beyond 500,000
cores for the largest simulations. NAMD uses the popular molecular
graphics program VMD for simulation setup and trajectory analysis, but
is also file-compatible with AMBER, CHARMM, and X-PLOR.

## Useful Links

  - [NAMD User Guide](http://www.ks.uiuc.edu/Research/namd/2.14/ug/)
  - [NAMD Tutorials](https://www.ks.uiuc.edu/Training/Tutorials/#namd)

## Using NAMD on ARCHER2
!!! note
    Our optimisation advice is based on the ARCHER2 4-cabinet preview
    system with the same node architecture as the current ARCHER2 service
    but a total of 1,024 compute nodes.

NAMD is freely available to all ARCHER2 users.

```
------------------- /work/y07/shared/archer2-lmod/apps/core --------------------
   namd/2.14-nosmp    namd/2.14 (D)
```

ARCHER2 has two versions of NAMD available: no-SMP (```namd/2.14-nosmp```)
or SMP (```namd/2.14```). The SMP (Shared Memory Parallelism) build of NAMD
introduces threaded parallelism to address memory limitations. The no-SMP
build will typically provide the best performance but most users will require
SMP in order to cope with high memory requirements.


### Running MPI only NAMD jobs

Using no-SMP NAMD will run jobs with only MPI processes and will not introduce
additional threaded parallelism. This is the simplest approach to running
NAMD jobs and is likely to give the best performance unless simulations
are limited by high memory requirements.

The following script will run a pure MPI NAMD MD job using 4 nodes (i.e.
128x4 = 512 MPI parallel processes).

=== "Full system"
    ```
    #!/bin/bash

    # Request four nodes to run a job of 512 MPI tasks with 128 MPI
    # tasks per node, here for maximum time 20 minutes.

    #SBATCH --job-name=namd-nosmp
    #SBATCH --nodes=4
    #SBATCH --tasks-per-node=128
    #SBATCH --cpus-per-task=1
    #SBATCH --time=00:20:00

    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    module load namd/2.14-nosmp

    srun --distribution=block:block --hint=nomultithread namd2 input.namd
    ```

### Running SMP NAMD jobs

If your jobs runs out of memory, then using the SMP version of NAMD will
reduce the memory requirements. This involves launching a combination of
MPI processes for communication and worker threads which perform computation.

The following script will run a SMP NAMD MD job using 4 nodes with 8 MPI
communication processes per node and 16 worker threads per communication process
(i.e. a fully-occupied node with all 512 cores populated with processes).

=== "Full system"
    ```
    #!/bin/bash
    #SBATCH --job-name=namd-smp
    #SBATCH --tasks-per-node=8
    #SBATCH --cpus-per-task=16
    #SBATCH --nodes=4
    #SBATCH --time=00:20:00

    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Load the relevant modules
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

**How do I choose an optimal choice of MPI processes and worker threads for my simulations?**
The optimal choice for the numbers of MPI processes and worker threads
per node depends on the data set and the number of compute nodes. Before
running large production jobs, it is worth experimenting with these parameters
to find the optimal configuration for your simulation.

We recommend that users match the ARCHER2 NUMA architecture, with 8 NUMA
regions per node and 16 cores per region, to find the optimal balance of
thread and process parallelism. For example, the above submission script
specifies 8 MPI communication processes per node and 16 worker threads per
communication process which places 1 MPI process per NUMA region on each node.

!!! note
    To ensure fully occupied nodes with the SMP build of NAMD and match the NUMA
    region, the optimal values of (`tasks-per-node`, `cpus-per-task`) are likely
    to be (32,4), (16,8) or (8,16).


**How do I choose a value for the +ppn flag?**
The number of workers per communication process is specified by the +ppn
argument to NAMD, which is set here to equal cpus-per-task - 1, to leave a
CPU-core free for the associated MPI process.

We recommend that users reserve a thread per process to improve the scalability.
Reserving this thread on a many-cores-per-node architecture like ARCHER2 will
reduce the communication between threads and improve the scalability.


## Compiling NAMD

The latest instructions for building NAMD on ARCHER2 may be found in
the GitHub repository of build instructions.

[ARCHER2 Full System](https://github.com/hpc-uk/build-instructions/blob/main/apps/NAMD/build_namd_2.14_archer2_gcc11_cmpich8.md)
