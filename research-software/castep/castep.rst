CASTEP
======

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

`CASTEP <http://www.castep.org>`__  is a leading code for calculating the
properties of materials from first principles. Using density functional theory,
it can simulate a wide range of properties of materials proprieties including
energetics, structure at the atomic level, vibrational properties, electronic
response properties etc. In particular it has a wide range of spectroscopic
features that link directly to experiment, such as infra-red and Raman
spectroscopies, NMR, and core level spectra.

Useful Links
------------

* `CASTEP User Guides <http://www.castep.org/CASTEP/Documentation>`__
* `CASTEP Tutorials <http://www.castep.org/CASTEP/OnlineTutorials>`__
* `CASTEP Licensing <http://www.castep.org/CASTEP/GettingCASTEP>`__

Using CASTEP on ARCHER2
-----------------------

**CASTEP is only available to users who have a valid CASTEP licence.**

If you have a CASTEP licence and wish to have access to CASTEP on ARCHER2,
please make a request via the SAFE LINK REQUIRED.
Please have your license details to hand.

.. note::

  A CASTEP version 19 license gives access to CASTEP 19 and earlier versions,
  but an earlier license does not give access to CASTEP 19. There are therefore
  two separate access routes available: `CASTEP` and `CASTEP19`. Please
  select the right one for your license.


Running parallel CASTEP jobs
----------------------------

CASTEP can exploit multiple nodes on ARCHER2 and will generally be run in
exclusive mode over more than one node.

For example, the following script will run a CASTEP job using 4 nodes
(128x4  cores).

.. warning::

  The following SLURM script requires validation

::

   #!/bin/bash

   # Request 4 nodes with 128 MPI tasks per node for 20 minutes
   # Replace [budget code] below with your account code,
   # e.g. '--account=t01'

   #SBATCH --job-name=CASTEP
   #SBATCH --nodes=4
   #SBATCH --tasks-per-node=128
   #SBATCH --cpus-per-task=1
   #SBATCH --time=00:20:00
   
   #SBATCH --account=[budget code]

   # Load the CASTEP module, avoid any unintentional OpenMP threading by
   # setting OMP_NUM_THREADS, and launch the code.
   
   module load castep

   export OMP_NUM_THREADS=1
   srun castep.mpi test_calc


Hints and Tips
--------------


Compiling CASTEP
----------------

The latest instructions for building CASTEP on ARCHER2 may be found
at https://github.com/hpc-uk/build-instructions/tree/master/CASTEP