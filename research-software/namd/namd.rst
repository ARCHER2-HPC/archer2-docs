NAMD
====

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.


`NAMD <http://www.ks.uiuc.edu/Research/namd/>`_, recipient of a 2002 Gordon
Bell Award and a 2012 Sidney Fernbach Award, is a parallel molecular dynamics
code designed for
high-performance simulation of large biomolecular systems. Based on Charm++
parallel objects, NAMD scales to hundreds of cores for typical simulations
and beyond 500,000 cores for the largest simulations. NAMD uses the popular
molecular graphics program VMD for simulation setup and trajectory analysis,
but is also file-compatible with AMBER, CHARMM, and X-PLOR. 

Useful Links
------------


* `NAMD User Guide <http://www.ks.uiuc.edu/Research/namd/2.13/ug/>`__
* `NAMD Tutorials <http://www.ks.uiuc.edu/Training/Tutorials/index-all.html#namd>`__


Using NAMD on ARCHER2
---------------------

NAMD is freely available to all ARCHER2 users.


Running parallel NAMD jobs
--------------------------


The following script will run a NAMD MD job using 4 nodes
(128x4 cores) with MPI.

.. warning::

  The following SLURM script is provisional and should be verified

::

   #!/bin/bash

   # Request four nodes to run a job of 512 MPI tasks with 128 MPI
   # tasks per node, here for maximum time 20 minutes.
   # Remember to replace [budget code] below with your account code,
   # e.g., '--account=t01-nell'
   
   #SBATCH --job-name=namd_test
   #SBATCH --nodes=4
   #SBATCH --ntasks=512
   #SBATCH --tasks-per-node=128
   #SBATCH --cpus-per-core=1
   #SBATCH --time=00:20:00
   
   #SBATCH --account=[budget code]
   
   # Load the relevant NAMD module
   # and launch the executable

   module load namd/2020.1.2.3

   srun ... namd2 input.namd

