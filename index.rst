ARCHER2 User Documentation
==========================

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development. ARCHER2 is due to commence operation in 2020, replacing
  the current service ARCHER. For more information on ARCHER, please
  visit the `ARCHER web site <http://www.archer.ac.uk>`__.

ARCHER2 is the next generation UK National Supercomputing Service. You can find more
information on the service and the research it supports on the
`ARCHER2 website <https://www.archer2.ac.uk>`__.

The ARCHER2 Service is a world class advanced computing resource for UK researchers.
ARCHER2 is provided by `UKRI <https://www.ukri.org/>`__, `EPCC <https://www.epcc.ed.ac.uk/>`__,
`Cray (an HPE company) <https://www.cray.com/>`__ and the
`University of Edinburgh <https://www.ed.ac.uk/>`__.

What the documentation covers
-----------------------------

This is the documentation for the ARCHER2 service and includes:

Quick Start Guide
   The ARCHER2 quick start guide provides the minimum information for new users or users
   transferring from ARCHER. 

ARCHER2 User and Best Practice Guide
   Covers all aspects of use of the ARCHER2 supercomputing service. This includes fundamentals
   (required by all users to use the system effectively), best practice for getting the most
   out of ARCHER2, and more advanced technical topics.

Research Software
   Information on each of the centrally-installed research software packages.

Software Libraries
   Information on the centrally-installed software libraries. Most libraries work
   *as expected* so no additional notes are required however a small number require
   specific documentation

Data Analysis and Tools
   Information on data analysis tools and other useful utilities.

Essential Skills
   This section provides information and links on essential skills required to use
   ARCHER2 efficiently: *e.g.* using Linux command line, accessing help and documentation.

Contributing to the documentation
---------------------------------

The source for this documentation is publicly available in the
`ARCHER2 documentation Github repository <https://github.com/ARCHER2-HPC/archer2-docs>`__
so that anyone can contribute to improve the documentation for the service. Contributions
can be in the form of improvements or addtions to the content and/or addtion of Issues 
providing suggestions for how it can be improved.

Full details of how to contribute can be found in the README.rst file of the repository.

Credits
-------

This documentation draws on the `Cirrus Tier-2 HPC Documentation <https://cirrus.readthedocs.io>`__,
`Sheffield Iceberg Documentation <https://docs.hpc.shef.ac.uk/>`__ and the
`ARCHER National Supercomputing Service Documentation <http://www.archer.ac.uk/documentation/>`__.

We are also grateful to the Isambard Tier-2 HPC service for discussions on the combination of Github
and Sphinx technologies.

.. toctree::
   :maxdepth: 2
   :caption: Quick start

   quick-start/overview.rst
   quick-start/quickstart-users.rst
   quick-start/quickstart-developers.rst

.. toctree::
   :maxdepth: 2
   :caption: User and best practice guide

   user-guide/overview.rst
   user-guide/connecting.rst
   user-guide/data.rst
   user-guide/sw-environment.rst
   user-guide/scheduler.rst
   user-guide/io.rst
   user-guide/dev-environment.rst
   user-guide/containers.rst
   user-guide/python.rst
   user-guide/analysis.rst
   user-guide/debug.rst
   user-guide/profile.rst
   user-guide/tuning.rst

.. toctree::
   :maxdepth: 2
   :caption: Research software

   research-software/overview.rst
   research-software/castep/castep.rst
   research-software/chemshell/chemshell.rst
   research-software/code-saturne/code-saturne.rst
   research-software/cp2k/cp2k.rst
   research-software/elk/elk.rst
   research-software/fenics/fenics.rst
   research-software/gromacs/gromacs.rst
   research-software/lammps/lammps.rst
   research-software/mitgcm/mitgcm.rst
   research-software/mo-unified-model/mo-unified-model.rst
   research-software/namd/namd.rst
   research-software/nemo/nemo.rst
   research-software/nwchem/nwchem.rst
   research-software/onetep/onetep.rst
   research-software/openfoam/openfoam.rst
   research-software/qe/qe.rst
   research-software/vasp/vasp.rst

.. toctree::
   :maxdepth: 2
   :caption: Software libraries

   software-libraries/overview.rst

.. toctree::
   :maxdepth: 2
   :caption: Data analysis and tools

   data-tools/overview.rst

.. toctree::
   :maxdepth: 2
   :caption: Essential skills

   essentials/overview.rst
