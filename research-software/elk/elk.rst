ELK
===

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.


ELK is an all-electron full-potential linearised augmented-plane wave
(FP-LAPW) code with many advanced features. It was written originally at
Karl-Franzens-Universitt Graz as a milestone of the EXCITING EU Research
and Training Network.

Useful Links
------------

* ELK home page       http://elk.sourceforge.net
* ELK documentation   http://elk.sourceforge.net/#documentation

Using ELK on ARCHER2
--------------------

ELK is freely available to all users on ARCHER2.


.. warning::

  Module and version information are pending



Running parallel ELK jobs
-------------------------


MPI ELK jobs
^^^^^^^^^^^^

The following script will run an ELK job on 4 nodes (512 cores).

.. warning::

  This script is provisional and requires verification

::

  #!/bin/bash

  # Request 512 MPI tasks (4 nodes at 128 tasks per node) with a
  # maximum wall clock time limit of 20 minutes.

  #SBATCH --job-name=elk_job
  #SBATCH --nodes=4
  #SBATCH --tasks-per-node=128
  #SBATCH --cpus-per-task=1
  #SBATCH --time=00:20:00

  # Replace [budget code] below with your project code (e.g. t01)
  #SBATCH --account=[budget code]
  #SBATCH --partition=standard
  #SBATCH --qos=standard

  # Setup the batch environment
  module load epcc-job-env

  module load elk

  srun --cpu-bind=cores elk 


Mixed MPI/OpenMP ELK jobs
^^^^^^^^^^^^^^^^^^^^^^^^^

The following script will run an ELK job on 4 nodes, using 8 OpenMP threads and 16 MPI tasks per node.

.. warning::

  This script is provisional and requires verification

::

   #!/bin/bash

   # Request 4 nodes (using 8 threads and 16 MPI tasks per node) with a
   # maximum wall clock time limit of 20 minutes.
   # Replace [budget code] with your account code.

   #SBATCH --job-name=elk_job
   #SBATCH --nodes=4
   #SBATCH --ntasks=64
   #SBATCH --tasks-per-node=16
   #SBATCH --cpus-per-task=8
   #SBATCH --time=00:20:00

   #SBATCH --account=[budget code]
   #SBATCH --partition=standard
   #SBATCH --qos=standard

   export OMP_NUM_THREADS=8
   # Load the elk module
   # Launch the executable 
   # Input filename elk.in

   module -s restore /etc/cray-pe.d/PrgEnv-gnu
   module load elk

   srun elk 



Hints and Tips
--------------

Compiling ELK
-------------

The latest instructions for building ELK on ARCHER2 may be found in the GitHub 
repositry of build instructions.

https://github.com/hpc-uk/build-instructions/tree/main/ELK
