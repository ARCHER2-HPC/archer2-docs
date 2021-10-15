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

The following script will run a ONETEP job using 2 nodes (256 cores). It
assumes that the input file is called `text_calc.dat`.

```
#!/bin/bash

# Request 2 nodes with 128 MPI tasks per node for 20 minutes
# Replace [budget code] below with your account code,
# e.g. '--account=t01'

#SBATCH --job-name=ONETEP
#SBATCH --nodes=2
#SBATCH --tasks-per-node=128
#SBATCH --cpus-per-task=1
#SBATCH --time=00:20:00

# Replace [budget code] below with your project code (e.g. t01)
#SBATCH --account=[budget code] 
#SBATCH --partition=standard
#SBATCH --qos=standard

# Setup the job environment (this module needs to be loaded before any other modules)
module load epcc-job-env

# Load the ONETEP module
module load onetep

# Make sure that the stack settings are correct
ulimit -s unlimited
export OMP_STACKSIZE=64M
export OMP_NUM_THREADS=1

# Launch the executable
srun --distribution=block:block --hint=nomultithread onetep.archer2 test_calc > test_calc.out
```

## Hints and Tips

See the information in the [ONETEP documentation](https://www.onetep.org/Main/RunningONETEP), in particular the information on stack sizes.

## Compiling ONETEP

The latest instructions for building ONETEP on ARCHER2 may be found in
the GitHub repository of build instructions:

   - [Build instructions for ONETEP on
     GitHub](https://github.com/hpc-uk/build-instructions/tree/main/apps/ONETEP)
