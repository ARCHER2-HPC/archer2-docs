Code Saturne
============

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.


Code_Saturne solves the Navier-Stokes equations for 2D, 2D-axisymmetric
and 3D flows, steady or unsteady, laminar or turbulent, incompressible or
weakly dilatable, isothermal or not, with scalar transport if required.
Several turbulence models are available, from Reynolds-averaged models
to large-eddy simulation (LES) models. In addition, a number of specific
physical models are also available as "modules": gas, coal and heavy-fuel
oil combustion, semi-transparent radiative transfer, particle-tracking
with Lagrangian modeling, Joule effect, electrics arcs, weakly compressible
flows, atmospheric flows, rotor/stator interaction for hydraulic machines.


Useful Links
------------

* Code Saturne home page https://www.code-saturne.org/cms/
* Code Saturne user guides https://www.code-saturne.org/cms/documentation/guides
* Code Saturne users' forum https://www.code-saturne.org/forum/


Using Code Saturne on ARCHER2
-----------------------------

Code Saturne is released under the GNU General Public Licence v2 and so is freely available to all users on ARCHER2.

You can load Code_Saturne 6.0.5 for use by running the following command::

  module load code_saturne/6.0.5-gcc10


Running parallel Code Saturne jobs
----------------------------------

Ccde Saturne can exploit multiple nodes on ARCHER2 and will generally be run
in exclusive mode.

After setting up a case it should be initialized by running the following
command from the case directory, where *setup.xml* is the input file::

  code_saturne run --initialize --param setup.xml

This will create a directory named for the current date and time 
(e.g. 20201019-1636) inside the RESU directory. Inside the new directory
will be a script named *run_solver* which will need some additions to 
resemble the one shown below. You will need to add the ``#SBATCH`` options to 
set the job name, size and so on. You will also need to add 
the two ``module load`` commands, and ``srun --cpu-bind=cores``
as well as the ``--mpi`` option to the line executing ``./cs_solver`` to ensure
parallel execution on the compute nodes.

The script below will run a Code_Saturne job over 4 nodes (128 x 4 = 512 cores)
for a maximum of 20 minutes.

::

  #!/bin/bash
  #SBATCH --job-name=CSExample
  #SBATCH --time=0:20:00
  #SBATCH --nodes=4
  #SBATCH --tasks-per-node=128
  #SBATCH --cpus-per-task=1
  #SBATCH --account=[budget code]
  #SBATCH --partition=standard
  #SBATCH --qos=standard

  # Export paths here if necessary or recommended.
  export LD_LIBRARY_PATH="/opt/cray/pe/libsci/20.08.1.2/GNU/9.1/x86_64/lib":$LD_LI
  BRARY_PATH
  export LD_LIBRARY_PATH="/work/y07/shared/code_saturne/6.0.5-gcc10/lib":$LD_LIBRA
  RY_PATH

  module load /work/y07/shared/archer2-modules/modulefiles-cse/epcc-setup-env
  module load code_saturne

  export OMP_NUM_THREADS=1

  cd /path/to/case/RESU/[date]-[time]

  # Run solver.
  srun --cpu-bind=cores ./cs_solver --mpi $@
  export CS_RET=$?

  exit $CS_RET

The *run_solver* script can then be submitted to the batch system with

::

  sbatch run_solver

Hints and tips
--------------

Compiling Code Saturne
----------------------

Instructions detailing how Code_Saturne was built on ARCHER2 are available at https://github.com/hpc-uk/build-instructions/tree/main/Code_Saturne
