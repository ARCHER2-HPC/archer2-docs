# NAMD

!!! warning
    The ARCHER2 Service is not yet available. This documentation is in
    development.

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
  - [NAMD Tutorials](http://www.ks.uiuc.edu/Training/Tutorials/index-all.html#namd)

## Using NAMD on ARCHER2

NAMD is freely available to all ARCHER2 users.

## Running parallel NAMD jobs

The following script will run a NAMD MD job using 4 nodes (128x4 cores)
with MPI.

```
#!/bin/bash

# Request four nodes to run a job of 512 MPI tasks with 128 MPI
# tasks per node, here for maximum time 20 minutes.

#SBATCH --job-name=namd_test
#SBATCH --nodes=4
#SBATCH --tasks-per-node=128
#SBATCH --cpus-per-core=1
#SBATCH --time=00:20:00

# Replace [budget code] below with your project code (e.g. t01)
#SBATCH --account=[budget code] 
#SBATCH --partition=standard
#SBATCH --qos=standard

# Setup the job environment (this module needs to be loaded before any other modules)
module load epcc-job-env

module load namd

srun --cpu-bind=cores namd2 input.namd
```