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

Code Saturne is freely available to all users on ARCHER2.


.. warning::

  The exact version information and module job-name is pending.


Running parallel Code Saturne jobs
----------------------------------

Ccde Saturne can exploit multiple nodes on ARCHER2 and will generally be run
in exclusive mode.

For example, the following script will run a Code Saturne job using 4 nodes
(128x4  cores).

.. warning::

  The following SLURM script requires validation

::

   #!/bin/bash

   # Request 4 nodes with 128 MPI tasks per node for 20 minutes
   # Replace [budget code] below with your account code,
   # e.g. '--account=t01'

   #SBATCH --job-name=CodeSaturne
   #SBATCH --nodes=4
   #SBATCH --tasks-per-node=128
   #SBATCH --cpus-per-task=1
   #SBATCH --time=00:20:00
   
   #SBATCH --account=[budget code]

   # Load the Code Saturne mdoule and run

   module load code-saturne

   code_saturne run --param case.xml


Hints and tips
--------------

Compiling Code Saturne
----------------------
