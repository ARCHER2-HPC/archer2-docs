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

To determine the electronic ground state, VASP makes use of efficient iterative
matrix diagonalisation techniques, like the residual minimisation method with
direct inversion of the iterative subspace (RMM-DIIS) or blocked Davidson
algorithms. These are coupled to highly efficient Broyden and Pulay density
mixing schemes to speed up the self-consistency cycle.

Useful Links
------------

  - `VASP Manual <http://cms.mpi.univie.ac.at/vasp/vasp/vasp.html>`__
  - `VASP wiki <https://www.vasp.at/wiki/index.php/The_VASP_Manual>`__
  - `VASP Licensing <http://www.vasp.at/index.php/faqs/71-how-can-i-purchase-a-vasp-license>`__

Using VASP on ARCHER2
---------------------

**VASP is only available to users who have a valid VASP licence.**

If you have a VASP 5 or 6 licence and wish to have access to VASP on ARCHER2,
please make a request via the SAFE, see:

  - `How to request access to package groups <https://epcced.github.io/safe-docs/safe-for-users/#how-to-request-access-to-a-package-group>`__

Please have your license details to hand.

.. note::

  Both VASP 5 and VASP 6 are available on ARCHER2. You generally need
  a different licence for each of these versions.

Running parallel VASP jobs
--------------------------

To access VASP you should load the appropriate ``vasp`` module in your job submission
scripts.

VASP 5
~~~~~~

To load the default version of VASP 5, you would use:

::

   module load vasp/5

Once loaded, the executables are called:

* ``vasp_std`` - Multiple k-point version
* ``vasp_gam`` - GAMMA-point only version
* ``vasp_ncl`` - Non-collinear version


Once the module has been loaded, you can access the LDA and PBE pseudopotentials for
VASP on ARCHER2 at:

:: 

  $VASP_PSPOT_DIR


The following script will run a VASP job using 2 nodes (128x2, 256 total cores).

::

  #!/bin/bash

  # Request 2 nodes (256 MPI tasks at 128 tasks per node) for 20 minutes.   
  # Remember to replace [budget code] below with your account code,
  # e.g., '--account=t01-victoria'

  #SBATCH --job-name=VASP_test
  #SBATCH --nodes=4
  #SBATCH --ntasks=512
  #SBATCH --tasks-per-node=128
  #SBATCH --cpus-per-task=1
  #SBATCH --time=00:20:00

  #SBATCH --partition=standard
  #SBATCH --qos=standard
  
  #SBATCH --account=[budget code]
  #SBATCH --partition=standard
  #SBATCH --qos=standard
  
  # Load the VASP module, avoid any unintentional OpenMP threading by
  # setting OMP_NUM_THREADS, and launch the code.
  export OMP_NUM_THREADS=1
  module load vasp/5
  srun --cpu-bind=cores vasp_std

VASP 6
~~~~~~

To load the default version of VASP 6, you would use:

::

   module load vasp/6

Once loaded, the executables are called:

* ``vasp_std`` - Multiple k-point version
* ``vasp_gam`` - GAMMA-point only version
* ``vasp_ncl`` - Non-collinear version

Once the module has been loaded, you can access the LDA and PBE pseudopotentials for
VASP on ARCHER2 at:

:: 

  $VASP_PSPOT_DIR


The following script will run a VASP job using 2 nodes (128x2, 256 total cores) using
only MPI ranks and no OpenMP threading.

.. note::

  VASP 6 can make use of OpenMP threads in addition to running with pure MPI. We will
  add notes on performance and use of threading in VASP as information becomes 
  available.

::

  #!/bin/bash

  # Request 2 nodes (256 MPI tasks at 128 tasks per node) for 20 minutes.   
  # Remember to replace [budget code] below with your account code,
  # e.g., '--account=t01-victoria'

  #SBATCH --job-name=VASP_test
  #SBATCH --nodes=4
  #SBATCH --ntasks=512
  #SBATCH --tasks-per-node=128
  #SBATCH --cpus-per-task=1
  #SBATCH --time=00:20:00

  #SBATCH --partition=standard
  #SBATCH --qos=standard
  
  #SBATCH --account=[budget code]
  #SBATCH --partition=standard
  #SBATCH --qos=standard
  
  # Load the VASP module, avoid any unintentional OpenMP threading by
  # setting OMP_NUM_THREADS, and launch the code.
  export OMP_NUM_THREADS=1
  module load vasp/6
  srun --cpu-bind=cores vasp_std

Compiling VASP on ARCHER2
-------------------------

If you wish to compile your own version of VASP on ARCHER2 (either
VASP 5 or VASP 6) you can find information on how we compiled the
central versions in the build instructions GitHub repository. See:

   - `Build instructions for VASP on GitHub <https://github.com/hpc-uk/build-instructions/tree/main/VASP>`__

Hints and tips
--------------

.. note::

  We will add information on running VASP efficiently on ARCHER2
  as it becomes available.
