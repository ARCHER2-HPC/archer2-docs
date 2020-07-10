VASP
====

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

The `Vienna Ab initio Simulation Package (VASP) <http://www.vasp.at>`__ is
a computer program for atomic scale materials modelling, e.g. electronic
structure calculations and quantum-mechanical molecular dynamics, from
first principles.

VASP computes an approximate solution to the many-body Schrödinger equation,
either within density functional theory (DFT), solving the Kohn-Sham
equations, or within the Hartree-Fock (HF) approximation, solving the
Roothaan equations. Hybrid functionals that mix the Hartree-Fock approach
with density functional theory are implemented as well. Furthermore, Green's
functions methods (GW quasiparticles, and ACFDT-RPA) and many-body
perturbation theory (2nd-order Møller-Plesset) are available in VASP.

In VASP, central quantities, like the one-electron orbitals, the electronic
charge density, and the local potential are expressed in plane wave basis
sets. The interactions between the electrons and ions are described using
norm-conserving or ultrasoft pseudopotentials, or the projector-augmented-wave
method.

To determine the electronic groundstate, VASP makes use of efficient iterative
matrix diagonalisation techniques, like the residual minimisation method with
direct inversion of the iterative subspace (RMM-DIIS) or blocked Davidson
algorithms. These are coupled to highly efficient Broyden and Pulay density
mixing schemes to speed up the self-consistency cycle.

Useful Links
------------

* `VASP Manual <http://cms.mpi.univie.ac.at/vasp/vasp/vasp.html>`__
* `VASP Licensing <http://www.vasp.at/index.php/faqs/71-how-can-i-purchase-a-vasp-license>`__

Using VASP on ARCHER2
---------------------

**VASP is only available to users who have a valid VASP licence.**

If you have a VASP licence and wish to have access to VASP on ARCHER2
(you may need to speak to your supervisor or line manager to obtain
the appropriate license details).


.. warning::

  Include details of how to apply for access via te SAFE


Running parallel VASP jobs
--------------------------

To access VASP you should load the ``vasp`` module in your job submission
scripts:

::

   module add vasp

Once loaded, the executables are called:

* ``vasp_std`` - Multiple k-point version
* ``vasp_gam`` - GAMMA-point only version
* ``vasp_ncl`` - Non-collinear version


You can access the LDA and PBE pseudopotentials for VASP on ARCHER2 at:

:: 

   /vasp4/location/pot


The following script will run a VASP job using 4 nodes (128x4 cores).

.. warning::

  The following script is a draft and requires verification.

::

   #!/bin/bash

   # Request 4 nodes (512 MPI tasks at 128 tasks per node) for 20 minutes.   
   # Remember to replace [budget code] below with your account code,
   # e.g., '--account=t01-victoria'

   #SBATCH --job-name=VASP_test
   #SBATCH --nodes=4
   #SBATCH --ntasks=512
   #SBATCH --tasks-per-node=128
   #SBATCH --cpus-per-task=1
   #SBATCH --time=00:20:00
   
   #SBATCH --account=[budget code]
   
   
   # Load the relevant VASP module
   # and run the appropriate VASP executable (here 'vasp_std')

   module load vasp/5.4.3.2.1
   srun ... vasp_std


Hints and tips
--------------