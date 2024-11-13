# QChem

QChem is an ab initio quantum chemistry software package for fast and
accurate simulations of molecular systems, including electronic and
molecular structure, reactivities, properties, and spectra.

!!! important
    QChem is not part of the officially supported
    software on ARCHER2. While the ARCHER2 service desk is able to provide
    support for basic use of this software (e.g. access to software, writing
    job submission scripts) it does not generally provide detailed technical
    support for the software and you may be directed to seek support from
    other places if the service desk cannot answer the questions.

## Useful Links

- [QChem home site](https://www.q-chem.com/)
- [QChem documentation](https://manual.q-chem.com/)

## Using QChem on ARCHER2

ARCHER2 has a site licence for QChem.

## Running parallel QChem jobs

!!! important
    QChem parallelisation is only available on ARCHER2 by using multiple threads 
    within a single compute node. Multi-process and multi-node parallelisation 
    will not work on ARCHER2.

The following script will run QChem using 16 OpenMP threads using the input in
`hf3c.in`.

```slurm
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --time=1:0:0
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=16

# Replace [budget code] below with your project code (e.g. e05)
#SBATCH --account=[budget code]
#SBATCH --partition=standard
#SBATCH --qos=standard

module load other-software
module load qchem

export OMP_PLACES=cores
export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK
export SRUN_CPUS_PER_TASK=$SLURM_CPUS_PER_TASK

export SLURM_HINT="nomultithread"
export SLURM_DISTRIBUTION="block:block"

qchem -slurm -nt $OMP_NUM_THREADS hf3c.in hf3c.out
```

