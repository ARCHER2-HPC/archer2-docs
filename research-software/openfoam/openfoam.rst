OpenFOAM
========

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

OpenFOAM is an open-source toolbox for computational fluid dynamics.
OpenFOAM consists of generic tools to simulate complex physics for a
variety of fields of interest, from fluid flows involving chemical
reactions, turbulence and heat transfer, to solid dynamics,
electromagnetism and the pricing of financial options.

The core technology of OpenFOAM is a flexible set of modules written in C++.
These are used to build solvers and utilities to perform pre-processing
and post-processing tasks ranging from simple data manipulation to
visualisation and mesh processing.


Useful Links
------------

* OpenFOAM website        https://openfoam.org
* OpenFOAM documentation  https://www.openfoam.com/documentation/


Using OpenFOAM on ARCHER2
-------------------------

OpenFOAM is released under a GPL v3 license and is freely available to
all users on ARCHER2.

To use OpenFOAM on ARCHER2 you should first load the OpenFOAM module:

::

   module add openfoam
   
After that you need to source the ``etc/bashrc`` file provided by OpenFOAM:

::

   source $OPENFOAM_CURPATH/etc/bashrc

You should then be able to use OpenFOAM. The above commands will also need to
be added to any job/batch submission scripts you want to use to run OpenFOAM.


Available Versions
^^^^^^^^^^^^^^^^^^

You can see the versions of OpenFOAM available on ARCHER2 from the command line
with ``module avail openfoam``.

Running parallel OpenFOAM jobs
------------------------------


.. warning:: Example scripts are pending


Hints and tips
--------------


Compiling OpenFOAM
------------------
