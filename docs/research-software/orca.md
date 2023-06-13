# ORCA

ORCA is an ab initio quantum chemistry program package that contains modern electronic structure methods including density functional theory, many-body perturbation, coupled cluster, multireference methods, and semi-empirical quantum chemistry methods. Its main field of application is larger molecules, transition metal complexes, and their spectroscopic properties. ORCA is developed in the research group of Frank Neese. The free version is available only for academic use at academic institutions.

!!! important
    ORCA is not part of the officially supported
    software on ARCHER2. While the ARCHER2 service desk is able to provide
    support for basic use of this software (e.g. access to software, writing
    job submission scripts) it does not generally provide detailed technical
    support for the software and you may be directed to seek support from
    other places if the service desk cannot answer the questions.

## Useful Links

- [ORCA Forum](https://orcaforum.kofo.mpg.de/app.php/portal)

## Using ORCA on ARCHER2

ORCA is available for academic use on ARCHER2 only. If you wish to use ORCA for commercial
applications, you must contact the ORCA developers.

## Running parallel ORCA jobs

The following script will run an ORCA job on the ARCHER2
system using 256 MPI processes across 2 nodes, each MPI process will be placed on a separate
physical core. It assumes that the input file is `my_calc.inp`

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

module load other-software
module load orca

# Launch the ORCA calculation
#   * You must use "$ORCADIR/orca" so the application has the full executable path
#   * Do not use "srun" to launch parallel ORCA jobs as they use OpenMPI rather than Cray MPICH
#   * Remember to change the name of the input file to match your file name
$ORCADIR/orca my_calc.inp
```

