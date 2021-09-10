# ChemShell

ChemShell is a script-based chemistry code focusing on hybrid QM/MM
calculations with support for standard quantum chemical or force field
calculations. There are two versions: an older Tcl-based version
Tcl-ChemShell and a more recent python-based version Py-ChemShell.

The advice from <https://www.chemshell.org/licence> on the difference
is:

> We regard Py-ChemShell 19.0 as suitable for production calculations on
> materials systems, although you will find its feature set is more
> limited than Tcl-ChemShell. We do not currently recommend Py-ChemShell
> for calculations on biological systems, as automated import of
> biomolecular force fields is scheduled for a future release.

## Useful Links

  - ChemShell home page <https://www.chemshell.org>
  - ChemShell documentation <https://www.chemshell.org/documentation>
  - ChemShell forums <https://www.chemshell.org/forum>

## Using ChemShell on ARCHER2

!!! warning
    The python-based version of ChemShell is not yet available on 
    ARCHER2

The python-based version of ChemShell is open-source and is freely
available to all users on ARCHER2.

The older version of Tcl-based ChemShell requires a license. Users with
a valid license should request access via the ARCHER2 SAFE.

## Running parallel ChemShell jobs

The following script will run a pure MPI Tcl-based ChemShell job using 8 
nodes (128x8 cores).

```
#!/bin/bash

# Slurm job options (job-name, compute nodes, job time)

#SBATCH --job-name=chemshell_test
#SBATCH --time=00:20:00
#SBATCH --nodes=1
#SBATCH --tasks-per-node=8
#SBATCH --cpus-per-task=128

# Replace [budget code] below with your project code (e.g. t01)
#SBATCH --account=[budget code]
#SBATCH --partition=standard
#SBATCH --qos=standard

# Setup the job environment (this module needs to be loaded before any other modules)

module restore PrgEnv-gnu
module load tcl-chemshell

# Set the number of threads to 1
#   This prevents any threaded system libraries from automatically
#   using threading.
export OMP_NUM_THREADS=1
 
  srun --distribution=block:block --hint=nomultithread chemsh.x input.chm > output.log
```
