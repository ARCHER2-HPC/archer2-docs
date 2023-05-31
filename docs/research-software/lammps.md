# LAMMPS

[LAMMPS](http://lammps.sandia.gov/) (Large-scale Atomic/Molecular Massively
Parallel Simulator) is a classical molecular dynamics code.
LAMMPS has potentials for solid-state materials (metals, semiconductors)
and soft matter (biomolecules, polymers), and coarse-grained or
mesoscopic systems. It can be used to model atoms or, more generically,
as a parallel particle simulator at the atomic, mesoscopic, or continuum
scale.

## Useful Links

  - [LAMMPS Documentation](https://lammps.sandia.gov/doc/Manual.html)
  - [LAMMPS Mailing list details](https://lammps.sandia.gov/mail.html)

## Using LAMMPS on ARCHER2

LAMMPS is freely available to all ARCHER2 users.

The centrally installed version of LAMMPS is compiled with all the
standard packages included: `ASPHERE`,
`BODY`,
`CLASS2`,
`COLLOID`,
`COMPRESS`,
`CORESHELL`,
`DIPOLE`,
`GRANULAR`,
`KSPACE`,
`MANYBODY`,
`MC`, `MISC`,
`MOLECULE`,
`OPT`, `PERI`,
`QEQ`,
`REPLICA`,
`RIGID`,
`SHOCK`,
`SNAP`, `SRD`.

We do not install any `USER` packages. If
you are interested in a `USER` package, we
would encourage you to try to compile your own version and we can help
out if necessary (see below).

!!! important
    The `lammps` modules reset the CPU frequency to the highest possible value
    (2.25 GHz) as this generally achieves the best balance of performance to 
    energy use. You can change this setting by following the instructions in the
    [Energy use section](../user-guide/energy.md) of the User Guide.

## Running parallel LAMMPS jobs

LAMMPS can exploit multiple nodes on ARCHER2 and will generally be run
in exclusive mode using more than one node.

For example, the following script will run a LAMMPS MD job using 4 nodes
(128x4 cores) with MPI only.

```slurm
#!/bin/bash

#SBATCH --job-name=lammps_test
#SBATCH --nodes=4
#SBATCH --ntasks-per-node=128
#SBATCH --cpus-per-task=1
#SBATCH --time=00:20:00

# Replace [budget code] below with your project code (e.g. t01)
#SBATCH --account=[budget code] 
#SBATCH --partition=standard
#SBATCH --qos=standard

module load lammps

# Ensure the cpus-per-task option is propagated to srun commands
export SRUN_CPUS_PER_TASK=$SLURM_CPUS_PER_TASK

srun --distribution=block:block --hint=nomultithread lmp -i in.test -l out.test
```

## Compiling LAMMPS

The large range of optional packages available for LAMMPS, and
opportunity for extensibility, may mean that it is convenient for users
to compile their own copy. In practice, LAMMPS is relatively easy to
compile, so we encourage users to have a go.

Compilation instructions for LAMMPS on ARCHER2 can be found on GitHub:

   - [Build instructions for LAMMPS on
     GitHub](https://github.com/hpc-uk/build-instructions/tree/main/apps/LAMMPS)

