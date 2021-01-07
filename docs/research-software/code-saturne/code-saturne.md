# Code\_Saturne

!!! warning
    The ARCHER2 Service is not yet available. This documentation is in
    development.

Code\_Saturne solves the Navier-Stokes equations for 2D, 2D-axisymmetric
and 3D flows, steady or unsteady, laminar or turbulent, incompressible
or weakly dilatable, isothermal or not, with scalar transport if
required. Several turbulence models are available, from
Reynolds-averaged models to large-eddy simulation (LES) models. In
addition, a number of specific physical models are also available as
"modules": gas, coal and heavy-fuel oil combustion, semi-transparent
radiative transfer, particle-tracking with Lagrangian modeling, Joule
effect, electrics arcs, weakly compressible flows, atmospheric flows,
rotor/stator interaction for hydraulic machines.

## Useful Links

  - Code\_Saturne home page <https://www.code-saturne.org/cms/>
  - Code\_Saturne user guides
    <https://www.code-saturne.org/cms/documentation/guides>
  - Code\_Saturne users' forum <https://www.code-saturne.org/forum/>

## Using Code\_Saturne on ARCHER2

Code\_Saturne is released under the GNU General Public Licence v2 and so
is freely available to all users on ARCHER2.

You can load Code\_Saturne 6.0.5 for use by running the following
command:

```
module load code_saturne/6.0.5-gcc10
```

## Running parallel Code\_Saturne jobs

After setting up a case it should be initialized by running the
following command from the case directory, where *setup.xml* is the
input file:

```
code_saturne run --initialize --param setup.xml
```

This will create a directory named for the current date and time (e.g.
20201019-1636) inside the RESU directory. Inside the new directory will
be a script named *run\_solver*. You may alter this to resemble the
script below, or you may wish to simply create a new one with the
contents shown.

If you wish to alter the existing *run\_solver* script you will need to
add all the `#SBATCH` options shown to set the job name, size and so on.
You should also add the two `module` commands, and
`srun --distribution=block:block --hint=nomultithread` as well as the `--mpi` option to the line executing
`./cs_solver` to ensure parallel execution on the compute nodes. The
`export LD_LIBRARY_PATH=...` and `cd` commands are redundant and may be
retained or removed.

This script will run an MPI-only Code\_Saturne job over 4 nodes (128 x 4
= 512 cores) for a maximum of 20 minutes.

```slurm
#!/bin/bash
#SBATCH --export=none
#SBATCH --job-name=CSExample
#SBATCH --time=0:20:0
#SBATCH --nodes=4
#SBATCH --tasks-per-node=128
#SBATCH --cpus-per-task=1

# Replace [budget code] below with your project code (e.g. t01)
#SBATCH --account=[budget code]
#SBATCH --partition=standard
#SBATCH --qos=standard

# Setup the batch environment
module load epcc-job-env

module load code_saturne

# Prevent threading.
export OMP_NUM_THREADS=1

# Run solver.
srun --distribution=block:block --hint=nomultithread ./cs_solver --mpi $@
```

The script can then be submitted to the batch system with `sbatch`.

## Compiling Code\_Saturne

The latest instructions for building Code\_Saturne on ARCHER2 may be found in
the GitHub repository of build instructions:

   - [Build instructions for Code\_Saturne on
     GitHub](https://github.com/hpc-uk/build-instructions/tree/main/Code_Saturne)

