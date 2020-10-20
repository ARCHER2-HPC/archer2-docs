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

.. warning::

  This script is provisional and requires verification

::

   #!/bin/bash

   # Request 512 MPI tasks (4 nodes at 128 tasks per node) with a
   # maximum wall clock time limit of 20 minutes.
   # Replace [budget code] with your account code, e.g.,
   # '--account=t01-brenda'

   #SBATCH --job-name=elk_job
   #SBATCH --nodes=4
   #SBATCH --ntasks=512
   #SBATCH --tasks-per-node=128
   #SBATCH --cpus-per-task=1
   #SBATCH --time=00:20:00

   #SBATCH --account=[budget code]
   #SBATCH --partition=standard
   #SBATCH --qos=standard

   # Load the elk module
   # Launch the executable

   module load elk/1.2.3.4
   srun elk MY_ELK_INPUT.in


Mixed MPI/OpenMP ELK jobs
^^^^^^^^^^^^^^^^^^^^^^^^^

Hints and Tips
--------------

Compiling ELK
-------------
