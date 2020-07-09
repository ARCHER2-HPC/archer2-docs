CP2K
====

`CP2K <https://www.cp2k.org/>`__ is a quantum chemistry and solid state
physics software package that can perform atomistic simulations of solid
state, liquid, molecular, periodic, material, crystal, and biological systems.
CP2K provides a general framework for different modelling methods
such as DFT using the mixed Gaussian and plane waves approaches GPW and GAPW.
Supported theory levels include DFTB, LDA, GGA, MP2, RPA, semi-empirical
methods (AM1, PM3, PM6, RM1, MNDO), and classical force fields
(AMBER, CHARMM). CP2K can do simulations of molecular dynamics,
metadynamics, Monte Carlo, Ehrenfest dynamics, vibrational analysis, core
level spectroscopy, energy minimisation, and transition state optimisation
using NEB or dimer method.

Useful links
------------

* `CP2K Reference Manual <https://manual.cp2k.org/#gsc.tab=0>`__
* `CP2K HOWTOs <https://www.cp2k.org/howto>`__
* `CP2K FAQs <https://www.cp2k.org/faq>`__

Using CP2K on ARCHER2
---------------------

CP2K is available through the `cp2k` module. MPI only `cp2k.popt`
and MPI/OpenMP Hybrid `cp2k.psmp` binaries are available.


For ARCHER2, CP2K has been compiled with the following optional features:
FFTW for fast Fourier transforms, `libint` to enable methods including
Hartree-Fock exchange, `libxsmm` for efficient small matrix multiplications,
`libxc` to provide a wider choice of exchange-correlation functionals,
ELPA for improved performance of matrix diagonalisation, PLUMED to allow
enhanced sampling methods, and SIRIUS for plane wave computations.

See https://www.cp2k.org/howto:compile for a full list of optional features.


If there is an optional feature not available, and which you would like,
please contact the Serive Desk. Experts may also wish to try to compile the
code themselves (see below for instructions).


Running parallel CP2K jobs
--------------------------

MPI only jobs
^^^^^^^^^^^^^

To run CP2K using MPI only, load the ``cp2k`` module and use the
``cp2k.popt`` executable.

For example, the following script will run a CP2K job using 4 nodes
(128x4 cores):

.. warning::

  This script is provisional and requires validation

::

   #!/bin/bash --login

   # Request 4 nodes using 128 cores per node for 128 MPI tasks per node.
   # Replace [budget code] below with your project code (e.g. t01)


   #SBATCH --job-name=CP2K_test
   #SBATCH --nodes=4
   #SBATCH --ntasks=512
   #SBATCH --tasks-per-node=128
   #SBATCH --cpus-per-task=1
   #SBATCH --time=00:20:00

   #SBATCH --account=[budget code]

   # Load the relevant CP2K module
   # Ensure that no libraries are inadvertently using threading
   # Launch the executable

   module load cp2k

   export OMP_NUM_THREADS=1

   srun cp2k.popt MYINPUT.inp


MPI/OpenMP hybrid jobs
^^^^^^^^^^^^^^^^^^^^^^

To run CP2K using MPI and OpenMP, load the ``cp2k`` module and use the
``cp2k.psmp`` executable.


.. warning::

  This script is provisional and requires validation


::

    #!/bin/bash

    # Request 4 nodes with 32 MPI tasks per node each using 8 threads;
    # note this means 128 MPI tasks in total.
    # Remember to replace [budget code] below with your account code,
    # e.g. '--account=t01'.

    #SBATCH --job-name=CP2K_test
    #SBATCH --nodes=4
    #SBATCH --ntasks=128
    #SBATCH --tasks-per-node=32
    #SBATCH --cpus-per-task=8
    #SBATCH --time=00:20:00

    #SBATCH --account=[budget code]

    # Load the relevant CP2K module
    # Ensure OMP_NUM_THREADS is consistent with cpus-per-task above
    # Launch the executable

    module load cp2k

    export OMP_NUM_THREADS=8

    srun cp2k.psmp MYINPUT.inp


Hints and Tips
--------------


Compiling CP2K
--------------

Details of how to compile CP2K on ARCHER2 are available
https://github.com/hpc-uk/build-instructions/tree/master/CP2K