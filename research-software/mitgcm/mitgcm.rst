MITgcm
======

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.


The Massachusetts Institute of Technology General Circulation Model (MITgcm)
is a numerical model designed for study of the atmosphere, ocean, and climate.
MITgcm's flexible non-hydrostatic formulation enables it to simulate fluid
phenomena over a wide range of scales; its adjoint capabilities enable it
to be applied to sensitivity questions and to parameter and state estimation
problems. By employing fluid equation isomorphisms, a single dynamical kernel
can be used to simulate flow of both the atmosphere and ocean.


Useful Links
------------

* MITgcm home page       http://mitgcm.org
* MITgcm documentation   https://mitgcm.readthedocs.io/en/latest/

Building MITgcm on ARCHER2
--------------------------

MITgcm is not available via a module on ARCHER2 as users will build their own
executables specific to the problem they are working on. However, we do provide
an optfile which will allow genmake2 to create Makefiles which will work on
ARCHER2.

You can obtain the MITgcm source code from the developers by cloning from
the GitHub repository with the command

::

  git clone https://github.com/MITgcm/MITgcm.git

You should then copy the ARCHER2 optfile into the MITgcm directories::

  cp /work/y07/shared/mitgcm/optfile/linux_amd64_gfortran_archer2 MITgcm/tools/build_options/

When you are building your code with this optfile, use the GNU environment with

::

  module restore PrgEnv-gnu

You should also set the following environment variables. ``MITGCM_ROOTDIR`` is
used to locate the source code and should point to the top MITgcm directory. 
Optionally, adding the MITgcm tools directory to your ``PATH`` environment
variable makes it easier to use tools such as genmake2, and the ``MITGCM_OPT``
environment variable makes it easier to refer to pass the optfile to genmake2.

::

  export MITGCM_ROOTDIR=/path/to/MITgcm
  export PATH=$MITGCM_ROOTDIR/tools:$PATH
  export MITGCM_OPT=$MITGCM_ROOTDIR/tools/build_options/linux_amd64_gfortran_archer2

When using genmake2 to create the Makefile, you will need to specify the
optfile to use. Other commonly used options might be to use extra source code
with the ``-mods`` option, and to enable MPI with ``-mpi``. You might then run
a command that resembles the following::

  genmake2 -mods /path/to/additional/source -mpi -optfile $MITGCM_OPT

You can read about the full set of options available to genmake2 by running

::

  genmake2 -help

Finally, you may then build your executable with the ``make depend``, ``make``
commands.

Running MITgcm on ARCHER2
-------------------------

Once you have built your executable you can write a script like the following
which will allow it to run on the ARCHER2 compute nodes. This example would run
a pure MPI MITgcm simulation over 2 nodes of 128 cores each for up to one hour.

::

  #!/bin/bash

  # Slurm job options (job-name, compute nodes, job time)
  #SBATCH --job-name=MITgcm-simulation
  #SBATCH --time=1:00:0
  #SBATCH --nodes=2
  #SBATCH --tasks-per-node=128
  #SBATCH --cpus-per-task=1

  # Replace [budget code] below with your project code (e.g. t01)
  #SBATCH --account=[budget code] 
  #SBATCH --partition=standard
  #SBATCH --qos=standard
  
  # Setup the job environment (this module needs to be loaded before any other modules)
  module load epcc-job-env

  # Set the number of threads to 1
  #   This prevents any threaded system libraries from automatically
  #   using threading.
  export OMP_NUM_THREADS=1

  # Launch the parallel job
  #   Using 256 MPI processes and 128 MPI processes per node
  #   srun picks up the distribution from the sbatch options
  srun --cpu-bind=cores ./mitgcmuv
