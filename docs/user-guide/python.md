# Using Python

Python is supported on ARCHER2 both for running intensive parallel jobs
and also as an analysis tool. This section describes how to use Python
in either of these scenarios.

The Python installations on ARCHER2 contain some of the most commonly
used packages. If you wish to install additional Python packages, we
recommend that you use the `pip` command, see the section entitled
[Installing your own Python packages (with pip)](./python.md#installing-your-own-python-packages-with-pip).

!!! important
    Python 2 is not supported on ARCHER2 as it has been deprecated since
    the start of 2020. 

!!! note
    When you log onto ARCHER2, no Python module is loaded by default. You
    will generally need to load the `cray-python` module to access the
    functionality described below.

## HPE Cray Python distribution

The recommended way to use Python on ARCHER2 is to use the HPE Cray
Python distribution.

The HPE Cray distribution provides Python 3 along with some of the most
common packages used for scientific computation and data analysis. These
include:

  - numpy and scipy - built using GCC against HPE Cray LibSci
  - mpi4py - built using GCC against HPE Cray MPICH
  - dask

The HPE Cray Python distribution can be loaded (either on the front-end
or in a submission script) using:

    module load cray-python
    
!!! tip
    The HPE Cray Python distribution is built using GCC compilers. If you wish
    to compile your own Python, C/C++ or Fortran code to use with HPE Cray
    Python, you should ensure that you compile using `PrgEnv-gnu` to make sure
    they are compatible.

## Installing your own Python packages (with pip)

Sometimes, you may need to setup a local custom Python environment such that it extends a centrally-installed `cray-python` module.
By extend, we mean being able to install packages locally that are not provided by `cray-python`. This is necessary because some Python
packages such as `mpi4py` must be built specifically for the ARCHER2 system and so are best provided centrally.

You can do this by creating a lightweight **virtual** environment where the local packages can be installed. Further, this environment
is created on top of an existing Python installation, known as the environment's **base** Python.

Select the base Python by loading the `cray-python` module that you wish to extend.

    auser@ln01:~> module load cray-python

Next, create the virtual environment within a designated folder.

    python -m venv --system-site-packages /work/t01/t01/auser/myvenv

In our example, the environment is created within a `myvenv` folder located on `/work`, which means the environment will be accessible
from the compute nodes. The `--system-site-packages` option ensures this environment is based on the currently loaded `cray-python`
module. See [https://docs.python.org/3/library/venv.html](https://docs.python.org/3/library/venv.html) for more details.

You're now ready to *activate* your environment.

    source /work/t01/t01/auser/myvenv/bin/activate

!!! tip
    The `myvenv` path uses a fictitious project code, `t01`, and username, `auser`. Please remember to replace those values
    with your actual project code and username. Alternatively, you could enter `${HOME/home/work}` in place of `/work/t01/t01/auser`.
    That command fragment expands `${HOME}` and then replaces the `home` part with `work`.

Installing packages to your local environment can now be done as follows.

    (myvenv) auser@ln01:~> python -m pip install <package name>

Running `pip` directly as in `pip install <package name>` will also work, but we show the `python -m` approach
as this is consistent with the way the virtual environment was created.

And when you have finished installing packages, you can deactivate the environment by running the `deactivate` command.

    (myvenv) auser@ln01:~> deactivate
    auser@ln01:~> 

The packages you have installed will only be available once the local environment has been activated. So, when running code that requires these packages,
you must first activate the environment, by adding the activation command to the submission script, as shown below.

```
#!/bin/bash --login

#SBATCH --job-name=myvenv
#SBATCH --nodes=2
#SBATCH --ntasks-per-node=64
#SBATCH --cpus-per-task=2
#SBATCH --time=00:10:00
#SBATCH --account=[budget code]
#SBATCH --partition=standard
#SBATCH --qos=standard

source /work/t01/t01/auser/myvenv/bin/activate

export SRUN_CPUS_PER_TASK=$SLURM_CPUS_PER_TASK

srun --distribution=block:block --hint=nomultithread python ${SLURM_SUBMIT_DIR}/myvenv-script.py
```

Lastly, the environment being extended does not have to come from one of the centrally-installed `cray-python` modules.
You can also create a local virtual environment based on one of the Machine Learning (ML) modules, e.g., `tensorflow`
or `pytorch`. One extra command is required; it is issued immediately after the `python -m venv ...` command.

    extend-venv-activate /work/t01/t01/auser/myvenv

The `extend-venv-activate` command merely adds some extra commands to the virtual environment's `activate` script,
ensuring that the python packages will be gathered from the local virtual environment, the ML module and from the
`cray-python` base module. All this means you would avoid having to install ML packages within your local area.

!!! note
    The ML modules are themselves based on `cray-python`. For example, `tensorflow/2.12.0` is based on the `cray-python/3.9.13.1` module.

## Running Python

### Example serial Python submission script

```
#!/bin/bash --login
    
#SBATCH --job-name=python_test
#SBATCH --ntasks=1
#SBATCH --time=00:10:00
    
# Replace [budget code] below with your project code (e.g. t01)
#SBATCH --account=[budget code]
#SBATCH --partition=serial
#SBATCH --qos=serial
   
# Load the Python module, ...
module load cray-python

# ..., or, if using local virtual environment
source <<path to virtual environment>>/bin/activate
    
# Run your Python program
python python_test.py
```

### Example mpi4py job submission script

Programs that have been parallelised with mpi4py can be run on the ARCHER2 compute nodes.
Unlike the serial Python submission script however, we must launch the Python interpreter
using `srun`. Failing to do so will result in Python running a single MPI rank only. 

```
#!/bin/bash --login
# Slurm job options (job-name, compute nodes, job time)
#SBATCH --job-name=mpi4py_test
#SBATCH --nodes=2
#SBATCH --ntasks-per-node=128
#SBATCH --cpus-per-task=1
#SBATCH --time=0:10:0

# Replace [budget code] below with your budget code (e.g. t01)
#SBATCH --account=[budget code]
#SBATCH --partition=standard
#SBATCH --qos=standard

# Load the Python module, ...
module load cray-python

# ..., or, if using local virtual environment
source <<path to virtual environment>>/bin/activate

# Pass cpus-per-task setting to srun
export SRUN_CPUS_PER_TASK=$SLURM_CPUS_PER_TASK

# Run your Python program
#   Note that srun MUST be used to wrap the call to python,
#   otherwise your code will run serially
srun --distribution=block:block --hint=nomultithread python mpi4py_test.py
```

!!! tip
    If you have installed your own packages you will need to activate your local Python
    environment within your job submission script as shown at the end of
    [Installing your own Python packages (with pip)](./python.md#installing-your-own-python-packages-with-pip).


## Using JupyterLab on ARCHER2

It is possible to view and run Jupyter notebooks from both login nodes 
and compute nodes on ARCHER2.

!!! note
    You can test such notebooks on the login nodes, but please do not attempt to
    run any computationally intensive work. Jobs may get killed once they hit a
    CPU limit on login nodes.

Please follow these steps.

1. Install JupyterLab in your work directory.
   ```
   module load cray-python
   export PYTHONUSERBASE=/work/t01/t01/auser/.local
   export PATH=$PYTHONUSERBASE/bin:$PATH
   # source <<path to virtual environment>>/bin/activate  # If using a virtualenvironment uncomment this line and remove the --user flag from the next
   
   pip install --user jupyterlab
   ```

2. If you want to test JupyterLab on the login node please go straight to step 3.
   To run your Jupyter notebook on a compute node, you first need to run an interactive session.
   ```
   srun --nodes=1 --exclusive --time=00:20:00 --account=<your_budget> \
        --partition=standard --qos=short --reservation=shortqos \
        --pty /bin/bash
   ```
   Your prompt will change to something like below.
   ```
   auser@nid001015:/tmp>
   ```
   In this case, the node id is `nid001015`. Now execute the following on the compute node.
   ```
   cd /work/t01/t01/auser # Update the path to your work directory
   export PYTHONUSERBASE=$(pwd)/.local
   export PATH=$PYTHONUSERBASE/bin:$PATH
   export HOME=$(pwd)
   module load cray-python
   # source <<path to virtual environment>>/bin/activate  # If using a virtualenvironment uncomment this line
   ```

3. Run the JupyterLab server.
   ```
   export JUPYTER_RUNTIME_DIR=$(pwd)
   jupyter lab --ip=0.0.0.0 --no-browser
   ```
   Once it's started, you will see a URL printed in the terminal window of 
   the form `http://127.0.0.1:<port_number>/lab?token=<string>`; we'll need this URL for
   step 6.

4. Please skip this step if you are connecting from a machine running Windows.
   Open a new terminal window on your laptop and run the following command.
   ```
   ssh <username>@login.archer2.ac.uk -L<port_number>:<node_id>:<port_number>
   ```   
   where `<username>` is your username, and `<node_id>` is the id of the node you're 
   currently on (for a login node, this will be `ln01`, or similar; on a compute 
   node, it will be a mix of numbers and letters). In our example, `<node_id>`
   is `nid001015`. Note, please use the same port number as that shown in the URL of
   step 3. This number may vary, likely values are 8888 or 8889.

5. Please skip this step if you are connecting from Linux or macOS. If you are connecting from Windows, you should use MobaXterm to configure an SSH tunnel as follows.
    - Click on the `Tunnelling` button above the MobaXterm terminal. Create a new tunnel by clicking on `New SSH tunnel` in the window that opens.
    - In the new window that opens, make sure the `Local port forwarding` radio button is selected.
    - In the `forwarded port` text box on the left under `My computer with MobaXterm`, enter the port number indicated in the JupyterLab server output (e.g., 8888 or 8890).
    - In the three text boxes on the bottom right under `SSH server` enter `login.archer2.ac.uk`, your ARCHER2 username and then `22`.
    - At the top right, under `Remote server`, enter the id of the login or compute node running the JupyterLab server and the associated port number.
    - Click on the `Save` button.
    - In the tunnelling window, you will now see a new row for the settings you just entered. If you like, you can give a name to the tunnel in the leftmost column to identify it.
    - Click on the small key icon close to the right for the new connection to tell MobaXterm which SSH private key to use when connecting to ARCHER2. You should tell it to use the same `.ppk` private key that you normally use when connecting to ARCHER2.
    - The tunnel should now be configured. Click on the small start button (like a play '>' icon) for the new tunnel to open it. You'll be asked to enter your ARCHER2 account password -- please do so.

6. Now, if you open a browser window locally, you should be able to navigate to the URL
   from step 3, and this should display the JupyterLab server. If JupyterLab is running
   on a compute node, the notebook will be available for the length of the interactive
   session you have requested.

!!! warning
    Please do not use the other http address given by the JupyterLab output,
    the one formatted `http://<node_id>:<port_number>/lab?token=<string>`. Your local
    browser will not recognise the `<node_id>` part of the address.

## Using Dask Job-Queue on ARCHER2

The Dask-jobqueue project makes it easy to deploy Dask on ARCHER2. 
You can find more information in 
[the Dask Job-Queue documentation](http://jobqueue.dask.org/en/latest/).

Please follow these steps:

1. Install Dask-Jobqueue

```
module load cray-python
export PYTHONUSERBASE=/work/t01/t01/auser/.local
export PATH=$PYTHONUSERBASE/bin:$PATH

pip install --user dask-jobqueue --upgrade
```

2. Using Dask

Dask-jobqueue creates a Dask Scheduler in the Python process where the cluster
object is instantiated. A script for running dask jobs on ARCHER2
might look something like this:

```
from dask_jobqueue import SLURMCluster
cluster = SLURMCluster(cores=128, 
                       processes=16,
                       memory='256GB',
                       queue='standard',
                       header_skip=['--mem'],
                       job_extra=['--qos="standard"'],
                       python='srun python',
                       project='z19',
                       walltime="01:00:00",
                       shebang="#!/bin/bash --login",
                       local_directory='$PWD',
                       interface='hsn0',
                       env_extra=['module load cray-python',
                                  'export PYTHONUSERBASE=/work/t01/t01/auser/.local/',
                                  'export PATH=$PYTHONUSERBASE/bin:$PATH',
                                  'export PYTHONPATH=$PYTHONUSERBASE/lib/python3.8/site-packages:$PYTHONPATH'])



cluster.scale(jobs=2)    # Deploy two single-node jobs

from dask.distributed import Client
client = Client(cluster)  # Connect this local process to remote workers

# wait for jobs to arrive, depending on the queue, this may take some time
import dask.array as da
x = …              # Dask commands now use these distributed resources
```

This script can be run on the login nodes and it submits the Dask jobs
to the job queue. Users should ensure that the computationally intensive
work is done with the Dask commands which run on the compute nodes.

The cluster object parameters specify the characteristics for running on a single compute node.
The header_skip option is required as we are running on exclusive nodes where you should not
specify the memory requirements, however Dask requires you to supply this option.

Jobs are be deployed with the cluster.scale command, where the jobs option sets
the number of single node jobs requested. Job scripts are generated
(from the cluster object) and these are submitted to the queue to begin 
running once the resources are available. You can check the status of the jobs by 
running `squeue -u $USER` in a separate terminal.

If you wish to see the generated job script you can use:

```
print(cluster.job_script())
```

