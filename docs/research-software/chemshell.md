# ChemShell

ChemShell is a script-based chemistry code focusing on hybrid QM/MM
calculations with support for standard quantum chemical or force field
calculations. There are two versions: an older Tcl-based version
Tcl-ChemShell and a more recent python-based version Py-ChemShell.

The advice from <https://www.chemshell.org/licence> on the difference
is:

> We consider Py-ChemShell 23.0 to be suitable for production 
> calculations on both materials systems and biomolecules, and
> recommend that new ChemShell users should use the Python-based 
> version.

> We continue to maintain the original Tcl-based version of ChemShell
> and distribute it on request. Tcl-ChemShell currently contains some 
> features that are not yet available in Py-ChemShell (but will be 
> soon!) including a QM/MM MD driver and multiple electronic state 
> calculations. At the present time if you need this functionality 
> you will need to obtain a licence for Tcl-Chemshell.

## Useful Links

  - ChemShell home page <https://www.chemshell.org>
  - ChemShell documentation <https://www.chemshell.org/documentation>
  - ChemShell forums <https://www.chemshell.org/forum>

## Using Py-ChemShell on ARCHER2

The python-based version of ChemShell is open-source and is freely
available to all users on ARCHER2. The version of Py-ChemShell 
pre-installed on ARCHER2 is compiled with NWChem and GULP as 
libraries.

!!! warning
    Py-ChemShell on ARCHER2 is compiled with 
    [GULP 6.0](http://gulp.curtin.edu.au/gulp/). This is a licenced 
    software that is free to use for academics. If you are not an 
    academic user (or if you are using Py-ChemShell for non-academic 
    work), please ensure that you have the correct GULP licence before 
    using GULP functionalities in py-ChemShell or make sure that you 
    are not using any of the GULP functionalities in your code (i.e., 
    do not set theory=GULP in your calculations).

### Running parallel Py-ChemShell jobs

Unlike most other ARCHER2 software packages, the Py-ChemShell module is built 
in such a way as to enable users to create and submit jobs to the compute 
nodes by running a `chemsh` script from the login node rather than by creating 
and submitting a Slurm submission script. Below is an example command for 
submitting a pure MPI Py-ChemShell job running on 8 nodes (128x8 cores) with 
the `chemsh` command:

```bash
    # Run this from the login node
    module load py-chemshell

    # Replace [budget code] below with your project code (e.g. t01)
    chemsh --submit               \
           --jobname pychmsh      \
           --account [budget code] \
           --partition standard   \
           --qos standard         \
           --walltime 0:10:0      \
           --nnodes 8             \
           --nprocs 1024          \ 
           py-chemshell-job.py
```

## Using Tcl-ChemShell on ARCHER2

The older version of Tcl-based ChemShell requires a license. Users with
a valid license should request access via the ARCHER2 SAFE.

### Running parallel Tcl-ChemShell jobs

The following script will run a pure MPI Tcl-based ChemShell job using 8 
nodes (128x8 cores).

```
#!/bin/bash

#SBATCH --job-name=lammps_test
#SBATCH --nodes=8
#SBATCH --ntasks-per-node=128
#SBATCH --cpus-per-task=1
#SBATCH --time=00:20:00

# Replace [budget code] below with your project code (e.g. t01)
#SBATCH --account=[budget code] 
#SBATCH --partition=standard
#SBATCH --qos=standard

module load tcl-chemshell/3.7.1

# Ensure the cpus-per-task option is propagated to srun commands
export SRUN_CPUS_PER_TASK=$SLURM_CPUS_PER_TASK

srun --distribution=block:block --hint=nomultithread chemsh.x input.chm
```
