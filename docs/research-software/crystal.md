# CRYSTAL

CRYSTAL is a general-purpose program for the study of crystalline solids. The
CRYSTAL program computes the electronic structure of periodic systems within
Hartree Fock, density functional or various hybrid approximations (global,
range-separated and double-hybrids). The Bloch functions of the periodic
systems are expanded as linear combinations of atom centred Gaussian
functions. Powerful screening techniques are used to exploit real space
locality. Restricted (Closed Shell) and Unrestricted (Spin-polarized)
calculations can be performed with all-electron and valence-only basis sets
with effective core pseudo-potentials. The current release is CRYSTAL23.

!!! important
    CRYSTAL is not part of the officially supported
    software on ARCHER2. While the ARCHER2 service desk is able to provide
    support for basic use of this software (e.g. access to software, writing
    job submission scripts) it does not generally provide detailed technical
    support for the software and you may be directed to seek support from
    other places if the service desk cannot answer the questions.

## Useful Links

- [CRYSTAL home site](https://www.crystal.unito.it)
- [CRYSTAL tutorials](http://tutorials.crystalsolutions.eu)
- [CRYSTAL licensing](http://www.crystalsolutions.eu)

## Using CRYSTAL on ARCHER2

CRYSTAL is only available to users who have a valid CRYSTAL license. You 
request access through SAFE:

- [How to request access to package groups](https://epcced.github.io/safe-docs/safe-for-users/#how-to-request-access-to-a-package-group-licensed-software-or-restricted-features)

Please have your license details to hand.

## Running parallel CRYSTAL jobs

The following script will run CRYSTAL using pure MPI for parallelisation
using 256 MPI processes, 1 per core across 2 nodes. It
assumes that the input file is tio2.d12

```slurm
#!/bin/bash
#SBATCH --nodes=2
#SBATCH --time=0:20:00
#SBATCH --ntasks-per-node=128

# Replace [budget code] below with your project code (e.g. e05)
#SBATCH --account=[budget code]
#SBATCH --partition=standard
#SBATCH --qos=standard

module load other-software
module load crystal/23-1.0.1
#or for the previous version use
#module load crystal/17-1.0.2

# Change this to the name of your input file
cp tio2.d12 INPUT

srun --hint=nomultithread --distribution=block:block MPPcrystal
```

An equivalent 2 node job using MPI+OpenMP parallelism with 4 threads per
MPI process, 64 MPI processes, 1 thread per core across 2 nodes would be:

```slurm
#!/bin/bash
#SBATCH --nodes=2
#SBATCH --time=0:20:00
#SBATCH --ntasks-per-node=32
#SBATCH --cpus-per-task=4

# Replace [budget code] below with your project code (e.g. e05)
#SBATCH --account=[budget code]
#SBATCH --partition=standard
#SBATCH --qos=standard

module load other-software
module load crystal/23-1.0.1

# Change this to the name of your input file
cp tio2.d12 INPUT

export OMP_NUM_THREADS=4
export OMP_PLACES=cores
export OMP_STACKSIZE=16M

srun --hint=nomultithread --distribution=block:block MPPcrystalOMP
```

## Tips and known issues

### CPU frequency

You should run some short (1 or 2 SCF cycles) jobs to test the scaling of your
job so you can decide on the balance between cost to your budget and the time
it takes to get a result. You now should include a few tests at different
clock rates as part of this process.

Based on a few simple tests we have run it is likely that jobs dominated by
building the Kohn-Sham matrix (SHELLX+MONMO3+NUMDFT in the output) will see
minimal energy savings and better performance at 2.25GHz. Jobs dominated by
the ScaLapack calls (MPP_DIAG in the output) may show useful energy savings at
2.0GHz.

### Out-of-memory errors

Long-running jobs may encounter unexpected errors of the form
```
slurmstepd: error: Detected 1 oom-kill event(s) in step 411502.0 cgroup.
```
These are related to a memory leak in the underlying libfabric communication
layer, which will be fixed in a future release. In the meantime, it should
be possible to work around the problem by adding
```
export FI_MR_CACHE_MAX_COUNT=0 
```
to the SLURM submission script.
