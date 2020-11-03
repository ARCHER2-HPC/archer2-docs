Using Python
============

Python is supported on ARCHER2 both for running intensive parallel jobs and also as an analysis tool.
This section describes how to use Python in either of these scenarios.

The Python installations on ARCHER2 contain some of the most commonly used modules. If you wish to install additional
Python modules, we recommend that you use the ``pip`` command **after** loading the ``cray-python`` module. This is
described in more detail below.

.. note::

   When you log onto ARCHER2, no Python module is loaded by default. You will generally need to load the ``cray-python``
   module to access the functionality described below. Running ``python`` without loading a module first will result
   in your using the operating system default Python which is likely not what you intend.
 
HPE Cray Python distribution
~~~~~~~~~~~~~~~~~~~~~~~~~~~

The recommended way to use Python on ARCHER2 is to use the HPE Cray Python distribution.

The HPE Cray distribution provides Python 3 along with some of the most common packages used for
scientific computation and data analysis. These include:

* numpy and scipy - built against HPE Cray LibSci
* mpi4py - built against HPE Cray MPICH
* dask

The HPE Cray Python distribution can be loaded (either on the front-end or in a submission
script) using:

::

    module load cray-python

.. note::

   The HPE Cray Python distribution provides Python 3. There is no Python 2 version as 
   Python 2 is now deprecated.

Adding your own packages
~~~~~~~~~~~~~~~~~~~~~~~~

If the packages you require are not included in the HPE Cray Python distribution,
further packages can be added using ``pip``. However, as the /home file systems are 
not available on the compute nodes, you will need to modify the default install
location that ``pip`` uses to point to a location on the /work file systems
(by default, ``pip`` installs into ``$HOME/.local``). To do this, you set the
``PYTHONUSERBASE`` environment variable to point to a location on /work, for 
example:

::

   export PYTHONUSERBASE=/work/t01/t01/auser/.local

You will also need to ensure that the location of commands installed by ``pip``
are available on the command line by modifying the ``PATH`` environment variable.
Once you have set ``PYTHONUSERBASE`` as described above, you can do this with the
command:

::

   export PATH=$PYTHONUSERBASE/bin:$PATH

We would recommend adding both of these commands to your ``$HOME/.bashrc`` file to ensure
they are set by default when you log in to ARCHER2.

Once, you have done this, you can use ``pip`` to add packages. This can be done using:

::

    pip install --user <package_name>

This uses the "-\\-user" flag to ensure the packages are installed in your user
directory. 

We recommend that you use the ``pipenv`` and/or ``virtualenv`` packages to manage your
Python environments. For information on how to do this see:

   - `Pipenv and Virtual Environments <https://docs.python-guide.org/dev/virtualenvs/>`__

Running Python on the compute nodes
===================================

In this section we provide example Python job submission scripts for a variety of 
scenarios of using Python on the ARCHER2 compute nodes.

Example serial Python submission script
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    #!/bin/bash --login

    #SBATCH --name=python_test
    #SBATCH --nodes=1
    #SBATCH --tasks-per-node=1
    #SBATCH --cpus-per-task=1
    #SBATCH --time=00:10:00

    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard
    
    # Load the Python module
    module load cray-python

    # Run your Python progamme
    python python_test.py

mpi4py
~~~~~~

Programmes that have been parallelised with mpi4py can be run on multiple
processors on ARCHER2. A sample submission script is given below. The primary
difference from the Python submission script in the previous section is that we
must run the programme using ``srun python my_prog.py`` instead of ``python
my_prog,py``. Failing to do so will cause a segmentation fault in your programme
when it reaches the line ``from mpi4py import MPI``.

::

    #!/bin/bash --login
    # Slurm job options (job-name, compute nodes, job time)
    #SBATCH --job-name=mpi4py_test
    #SBATCH --nodes=1
    #SBATCH --tasks-per-node=2
    #SBATCH --cpus-per-task=1
    #SBATCH --time=0:10:0

    # Replace [budget code] below with your budget code (e.g. t01)
    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Load the Python module
    module load cray-python

    # Run your Python programme
    # Note that srun MUST be used to wrap the call to python, otherwise an error
    # will occur
    srun python mpi4py_test.py

