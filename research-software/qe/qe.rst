Quantum Espresso
================

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

Quantum Espresso (QE) is an integrated
suite of open-source computer codes for electronic-structure calculations
and materials modeling at the nanoscale. It is based on density-functional
theory, plane waves, and pseudopotentials.

Useful Links
------------

* Quantum Espresso home page http://www.quantum-espresso.org/
* Quantum Espresso
  `User Guides <http://www.quantum-espresso.org/users-manual/>`__
* Quantum Espresso `Tutorials <http://www.quantum-espresso.org/tutorials/>`__

Using QE on ARCHER2
-------------------

QE is released under a GPL v2 license and is freely available to all ARCHER2
users.



Running parallel QE jobs
------------------------

For example, the following script will run a QE ``pw.x`` job using 4 nodes
(128x4 cores).

.. warning::

  The following SLURM script is a draft and requires verification

::

   #!/bin/bash

   # Request 4 nodes to run a 512 MPI task job with 128 MPI tasks per node.
   # The maximum walltime limit is set to be 20 minutes.
   # Remember to replace [budget code] below with your own account code,
   # e.g. '--account=t01-queenie`

   #SBATCH --job-name=qe_test
   #SBATCH --nodes=4
   #SBATCH --ntasks=512
   #SBATCH --ntasks-per-node=128
   #SBATCH --cpus-per-task=1
   #SBATCH --time=00:20:00
   
   #SBATCH --account=[budget code]
   #SBATCH --partition=standard
   #SBATCH --qos=standard
   
   # Load the relevant Quantum Espresso module

   module load qe

   srun ... pw.x < test_calc.in


Hints and tips
--------------

The QE module is set to load up the default QE-provided pseudo-
potentials. If you wish to use non-default pseudo-potentials,
you will need to change the ``ESPRESSO_PSEUDO`` variable to point
to the directory you wish. This can be done by adding the following
line **after** the module is loaded

::

  export ESPRESSO_PSEUDO /path/to/pseudo_potentials


Compiling QE
------------

The latest instructions for building QE on ARCHER2 can be found
in the GitHub repository of build instructions:

 - `Build instructions for Quantum Espresso <https://github.com/hpc-uk/build-instructions/blob/main/QuantumEspresso/qe66_archer2_gnu.md>`__
