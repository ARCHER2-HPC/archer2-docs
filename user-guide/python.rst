Using Python
============

Python for general users
------------------------

Cray Python 3 distribution
~~~~~~~~~~~~~~~~~~~~~~~~~~

Python 3 on ARCHER2 is provided by Cray.

The central installation provides many of the most common packages used for
scientific computation and data analysis. These include:

* numpy and scipy - built against Cray LibSci
* mpi4py - built against Cray MPT
* dask

The Python 3 module can be loaded (either on the front-end or in a submission
script) using:

::

    module load cray-python

Adding your own packages
~~~~~~~~~~~~~~~~~~~~~~~~

If the packages you require are not included in the central Python distribution,
further packages can easily be built on top using pip. This can be done using:

::

    pip install --user <package_name>

This uses the "-\\-user" flag to ensure the packages are installed in your user
directory. This means that you can have your own Python environment that is
independent of the central installation.

conda
~~~~~
This section on the ``conda`` tool is adapted from the NERSC Python documentation at `<https://docs.nersc.gov/programming/high-level-environments/python/>`__.

Creating conda environments
^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``conda`` tool allows you to build your own custom Python installation
through "environments".
First, create a conda environment using ``conda create``. Specify a name for
your environment using the "-n" flag, and the version of the Python interpreter
you want installed. For example, the following will create a Python 3.6
environment called "myenv":

::

    conda create -n myenv python=3.6

You will then be asked to confirm the package managements steps that will be
taken, along with the installation location.

Activating conda environments
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

There are two options for activating a conda environments: ``source activate``
and ``conda activate``.

``source activate`` is the only option for versions of conda prior to 4.6.

You can activate an environment with the name "myenv" with the command:

::

    source activate myenv

and then deactivate it with:

::

    source deactivate

``conda activate`` is available in conda 4.6 and later. It can be more complex
to use than ``source activate``, but has some advantages.
Before running ``conda activate`` for the first time, first run the command
``conda init``. This sets the current Python environment as the default. It does
this by adding lines to your ``.bashrc`` file that will be run at login. (These
lines are enclosed by the lines ``# >>> conda initialize >>>`` and
``# <<< conda initialize <<<``, and can be removed or edited manually if
needed.)

Having set your default environment, your custom environments can be activated
using:
::

    conda activate myenv

and deactivated using:
::

    conda deactivate

Installing Packages
^^^^^^^^^^^^^^^^^^^

conda can also be used to find and install packages into your environments.
For example, the following can be used to find and then install a package (in
this case, scipy):

::

    conda search scipy
    conda install scipy

Example Python submission script
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

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

