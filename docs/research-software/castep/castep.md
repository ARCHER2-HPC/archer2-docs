# CASTEP

!!! warning
    The ARCHER2 Service is not yet available. This documentation is in
    development.

[CASTEP](http://www.castep.org) is a leading code for calculating the
properties of materials from first principles. Using density functional
theory, it can simulate a wide range of properties of materials
proprieties including energetics, structure at the atomic level,
vibrational properties, electronic response properties etc. In
particular it has a wide range of spectroscopic features that link
directly to experiment, such as infra-red and Raman spectroscopies, NMR,
and core level spectra.

## Useful Links

   - [CASTEP User Guides](http://www.castep.org/CASTEP/Documentation)
   - [CASTEP Tutorials](http://www.castep.org/CASTEP/OnlineTutorials)
   - [CASTEP Licensing](http://www.castep.org/CASTEP/GettingCASTEP)

## Using CASTEP on ARCHER2

**CASTEP is only available to users who have a valid CASTEP licence.**

If you have a CASTEP licence and wish to have access to CASTEP on
ARCHER2, please make a request via the SAFE, see:

   - [How to request access to package
     groups](https://epcced.github.io/safe-docs/safe-for-users/#how-to-request-access-to-a-package-group)

Please have your license details to hand.

## Running parallel CASTEP jobs

The following script will run a CASTEP job using 2 nodes (256 cores). it
assumes that the input files have the file stem `text_calc`.

```
#!/bin/bash

# Request 2 nodes with 128 MPI tasks per node for 20 minutes
#SBATCH --job-name=CASTEP
#SBATCH --nodes=2
#SBATCH --tasks-per-node=128
#SBATCH --cpus-per-task=1
#SBATCH --time=00:20:00

# Replace [budget code] below with your project code (e.g. t01)
#SBATCH --account=[budget code]
#SBATCH --partition=standard
#SBATCH --qos=standard

# Setup the batch environment
module load epcc-job-env

# Load the CASTEP module, avoid any unintentional OpenMP threading by
# setting OMP_NUM_THREADS, and launch the code.
module load castep
export OMP_NUM_THREADS=1
srun -cpu-bind=cores castep.mpi test_calc
```

## Compiling CASTEP

The latest instructions for building CASTEP on ARCHER2 may be found in
the GitHub repository of build instructions:

   - [Build instructions for CASTEP on
     GitHub](https://github.com/hpc-uk/build-instructions/tree/master/CASTEP)
