LAMMPS
======

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.


`LAMMPS <http://lammps.sandia.gov/>`_, is a classical molecular dynamics code,
("Large-scale Atomic/Molecular Massively Parallel Simulator"). LAMMPS has
potentials for solid-state materials (metals, semiconductors) and soft matter
(biomolecules, polymers) and coarse-grained or mesoscopic systems.
It can be used to model atoms or, more generically, as a parallel particle
simulator at the atomic, mesoscopic, or continuum scale.

Useful Links
------------

* LAMMPS Documentation https://lammps.sandia.gov/doc/Manual.html 
* LAMMPS Mailing list details https://lammps.sandia.gov/mail.html

Using LAMMPS on ARCHER2
-----------------------

LAMMPS is freely available to all ARCHER2 users.

The centrally installed version of LAMMPS is compiled with all the
standard packages included: `ASPHERE`, `BODY`, `CLASS2`, `COLLOID`, 
`COMPRESS`, `CORESHELL`, `DIPOLE`, `GRANULAR`, `KSPACE`, `MANYBODY`,
'MC`, `MISC`, `MOLECULE`, `OPT`, `PERI`, `QEQ`, `REPLICA`, `RIGID`, 
`SHOCK`, `SNAP`, `SRD`.

We do not install any `USER` packages. If you are interested in a `USER`
package, we would encourage you to try to compile your own version
and we can help out if necessary (see below).


Running parallel LAMMPS jobs
----------------------------

LAMMPS can exploit multiple nodes on ARCHER2 and will generally be run in
exclusive mode using more than one node.

For example, the following script will run a LAMMPS MD job using 4 nodes
(128x4 cores) with MPI only.

.. warning::

  The following script is provisional and needs to be verified

::

   #!/bin/bash --login

   #SBATCH --job-name=lammps_test
   #SBATCH --nodes=4
   #SBATCH --ntasks=512
   #SBATCH --tasks-per-node=128
   #SBATCH --cpus-per-task=1
   #SBATCH --time=00:20:00
   
   # Replace [budget code] below with your project code (e.g. t01)
   #SBATCH --account=[budget code]
   #SBATCH --partition=standard
   #SBATCH --qos=standard
   
   # Load the relevant LAMMPS module
   module load lammps

   srun lmp -i in.test -o out.test


Hints and Tips
--------------

Compiling LAMMPS
----------------

The large range of optional packages availble for LAMMPS, and opportunity
for extensibility,  may mean that it is convenient for users to compile
their own copy. In practice, LAMMPS is relatively easy to compile, so we
encourage users to have a go.

Compilation instructions for LAMMPS on ARCHER2 can be found on GitHub:

https://github.com/hpc-uk/build-instructions/tree/master/LAMMPS

If you get stuck, please contact the Service Desk.

