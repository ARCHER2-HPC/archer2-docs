# CASINO

!!! note
    CASINO is not available as central install/module on ARCHER2 at this time. This 
    page provides tips on using CASINO on ARCHER2 for users who have obtained their 
    own copy of the code.

[CASINO](https://vallico.net/casinoqmc/) is a computer program system for performing
quantum Monte Carlo (QMC) electronic structure calculations that has been developed
by a group of researchers initially working in the Theory of Condensed Matter group in
the Cambridge University physics department, and their collaborators, over more than
20 years.  It is capable of calculating incredibly accurate solutions to the Schrödinger
equation of quantum mechanics for realistic systems built from atoms. 

## Useful Links

   - [CASINO User Manual](https://casinoqmc.net/casino_manual_dir/casino_manual.pdf)
   - [CASINO Licensing](https://vallico.net/casinoqmc/request-a-copy/)

## Compiling CASINO on ARCHER2

You should use the `linuxpc-gcc-slurm-parallel.archer2` configuration that is supplied
along with the CASINO source code to build on ARCHER2 and ensure that you build the
"Shm" (System-V shared memory) version of the code.

!!! bug
    The `linuxpc-cray-slurm-parallel.archer2` configuration produces a binary that
    crashes with a segfault and should not be used.

## Using CASINO on ARCHER2

The performance of CASINO on ARCHER2 is critically dependent on three things:

- The MPI transport layer used: UCX is required for good scaling to multiple nodes
- The number of cores that share System-V shared memory segments: 8 or 16 cores are
  the best performing choices. If memory efficiency is critical, then 32 cores gives
  good performance. More than 32 cores sharing a memory segment gives poor performance.
- Ensuring that MPI processes are pinned to cores in a sequential manner so cores that 
  are sharing shared memory segments are located as close to each other as possible.

Next, we show how to make sure that the MPI transport layer is set to UCX, 
how to set the number of cores sharing the System-V shared memory segments and
how to pin MPI processes sequentially to cores.

Finally, we provide a job submission script that demonstrates all
these options together.

### Setting the MPI transport layer to UCX

In your job submission script that runs CASINO you switch to using UCX as the MPI
transport layer by including the following lines *before* you run CASINO (i.e. before
the `srun` command that launches the CASINO executable):

```
module load PrgEnv-gnu
module load craype-network-ucx
module load cray-mpich-ucx
```

### Setting the number of cores sharing memory

In your job submission script you set the number of cores sharing memory segments 
by setting the `CASINO_NUMABLK` environment variable before you run CASINO. For example,
to specify that there should be shared memory segments each shared between 16 cores, you
would use:

```
export CASINO_NUMABLK=16
```

!!! tip
    If you do not set `CASINO_NUMABLK` then CASINO will use the default of all cores on 
    a node (the equivalent of setting it to 128) which will give very poor performance so
    you should always set this environment variable. Setting `CASINO_NUMABLK` to 8 or 16
    cores gives the best performance. 32 cores is acceptable if you want to maximise
    memory efficiency. Using 64 and 128 gives poor performance.

### Pinning MPI processes sequentially to cores

For shared memory segments to work efficiently MPI processes must be pinned sequentially
to cores on compute nodes (so that cores sharing memory are close in the node memory hierarchy).
To do this, you add the following options to the `srun` command in your job script that runs
the CASINO executable:

```
--distribution=block:block --hint=nomultithread
```

### Example CASINO job submission script

The following script will run a CASINO job using 16 nodes (2048 cores).

```
#!/bin/bash

# Request 16 nodes with 128 MPI tasks per node for 20 minutes
#SBATCH --job-name=CASINO
#SBATCH --nodes=16
#SBATCH --tasks-per-node=128
#SBATCH --cpus-per-task=1
#SBATCH --time=00:20:00

# Replace [budget code] below with your project code (e.g. t01)
#SBATCH --account=[budget code]
#SBATCH --partition=standard
#SBATCH --qos=standard

# Ensure we are using UCX as the MPI transport layer
module load PrgEnv-gnu
module load craype-network-ucx
module load cray-mpich-ucx

# Set CASINO to share memory across 16 core blocks
export CASINO_NUMABLK=16

# Set the location of the CASINO executable - this must be on /work
#   Replace this with the path to your compiled CASINO binary
CASINO_EXE=/work/t01/t01/auser/CASINO/bin_qmc/linuxpc-gcc-slurm-parallel.archer2/Shm/opt/casino

# Launch CASINO with MPI processes pinned to cores in a sequential order
srun --distribution=block:block --hint=nomultithread ${CASINO_EXE}
```

## CASINO performance on ARCHER2

We have run the *benzene_dimer* benchmark on ARCHER2 with the following configuration:

- Compiler arch: `linuxpc-gcc-slurm-parallel.archer2`, "Shm" version
- Compiler: GCC 10.2.0
- MPI Library: HPE Cray MPICH 8.1.4
- MPI transport layer: UCX
- 128 MPI processes per node

Timings are reported as time taken for 100 equilibration steps in DMC calculation.

### CASINO_NUMABLK=8

| Nodes | Time taken (s) | Speedup |
|------:|---------------:|--------:|
|     1 |         289.90 |     1.0 |
|     2 |         154.93 |     1.9 |
|     4 |          81.06 |     3.6 |
|     8 |          41.44 |     7.0 |
|    16 |          23.16 |    12.5 |
