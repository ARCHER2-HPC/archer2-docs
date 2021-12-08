# CRYSTAL

CRYSTAL is a general-purpose program for the study of crystalline solids. The
CRYSTAL program computes the electronic structure of periodic systems within
Hartree Fock, density functional or various hybrid approximations (global,
range-separated and double-hybrids). The Bloch functions of the periodic
systems are expanded as linear combinations of atom centred Gaussian
functions. Powerful screening techniques are used to exploit real space
locality. Restricted (Closed Shell) and Unrestricted (Spin-polarized)
calculations can be performed with all-electron and valence-only basis sets
with effective core pseudo-potentials. The current release is CRYSTAL17.

## Useful Links

- [CRYSTAL home site](https://www.crystal.unito.it)
- [CRYSTAL tutorials](http://tutorials.crystalsolutions.eu)
- [CRYSTAL licensing](http://www.crystalsolutions.eu)

## Using CRYSTAL on ARCHER2

CRYSTAL is only available to users who have a valid CRYSTAL license. You 
request access through SAFE:

- [How to request access to package groups](https://epcced.github.io/safe-docs/safe-for-users/#how-to-request-access-to-a-package-group)

Please have your license details to hand.

## Running parallel CRYSTAL jobs

!!! important
    CRYSTAL is not yet available on the full ARCHER2 system. We are 
    working with the CRYSTAL developers to get the software 
    deployed as soon as possible.

The following script will run a CRYSTAL job on the ARCHER2 4-cabinet
system using 256 cores (2 nodes). It assumes that the input file is tio2.d12

```slurm
#!/bin/bash
#SBATCH --nodes=2
#SBATCH --ntasks-per-node=128
#SBATCH --cpus-per-task=1
#SBATCH --time=0:20:00

# Replace [budget code] below with your project code (e.g. e05)
#SBATCH --account=[budget code]
#SBATCH --partition=standard
#SBATCH --qos=standard
#SBATCH --export=none

module load epcc-job-env
module load other-software
module load crystal

# Change this to the name of your input file
cp tio2.d12 INPUT

srun --hint=nomultithread --distribution=block:block MPPcrystal
```

## Known Issues

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
