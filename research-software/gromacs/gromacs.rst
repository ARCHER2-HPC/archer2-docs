GROMACS
=======


.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

`GROMACS <http://www.gromacs.org/>`__  is a versatile package to
perform molecular dynamics, i.e. simulate the Newtonian equations of
motion for systems with hundreds to millions of particles.  It is
primarily designed for biochemical molecules like proteins, lipids
and nucleic acids that have a lot of complicated bonded interactions,
but since GROMACS is extremely fast at calculating the nonbonded
interactions (that usually dominate simulations) many groups are
also using it for research on non-biological systems, e.g. polymers.


Useful Links
------------

* `GROMACS User Guides <http://manual.gromacs.org/documentation/>`__
* `GROMACS Tutorials <http://www.gromacs.org/Documentation/Tutorials>`__

Using GROMACS on ARCHER2
------------------------

GROMACS is Open Source software and is freely available to all users.
Four versions are available:

* Serial/shared memory, single precision: gmx
* Parallel MPI/OpenMP, single precision: gmx_mpi


Running parallel GROMACS jobs
-----------------------------

Running MPI only jobs
^^^^^^^^^^^^^^^^^^^^^

The following script will run a GROMACS MD job using 4 nodes
(128x4 cores) with pure MPI.

.. warning:: 

  The following SLURM script is provisional and requires verification

::

   #!/bin/bash
   
   # Replace [budget code] below with your project code (e.g. t01)

   #SBATCH --job-name-mdrun_test
   #SBATCH --nodes=4
   #SBATCH --ntasks=512
   #SBATCH --tasks-per-node=128
   #SBATCH --cpus-per-task=1
   #SBATCH --time=00:20:00
   
   #SBATCH --account=[budget code]
   
   
   # Load the relevant GROMACS module

   module load gromacs/2020.1.2.3

   export OMP_NUM_THREADS=1 
   srun gmx_mpi mdrun -s test_calc.tpr


Running hybrid MPI/OpenMP jobs
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The following script will run a GROMACS MD job using 4 nodes
(128x4 cores) with 6 MPI processes per node (24 MPI processes in
total) and 6 OpenMP threads per MPI process.


.. warning:: 

  The following SLURM script is provisional and requires verification


::

   #!/bin/bash
   
   # Replace [budget code] below with your project code (e.g. t01)

   #SBATCH --job-name-mdrun_test
   #SBATCH --nodes=4
   #SBATCH --ntasks=64
   #SBATCH --tasks-per-node=16
   #SBATCH --cpus-per-task=8
   #SBATCH --time=00:20:00

   #SBATCH --account=[budget code]
   
   # Load the relevant GROMACS module

   module load gromacs/2020.1.2.3.4

   export OMP_NUM_THREADS=8
   srun gmx_mpi mdrun -s test_calc.tpr


Hints and Tips
--------------


Compiling Gromacs
-----------------
