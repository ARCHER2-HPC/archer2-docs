# Using Python

Python is supported on ARCHER2 both for running intensive parallel jobs
and also as an analysis tool. This section describes how to use Python
in either of these scenarios.

The Python installations on ARCHER2 contain some of the most commonly
used modules. If you wish to install additional Python modules, we
recommend that you use the `pip` command **after** loading the
`cray-python` module. This is described in more detail below.

!!! note
    When you log onto ARCHER2, no Python module is loaded by default. You
    will generally need to load the `cray-python` module to access the
    functionality described below. Running `python` without loading a module
    first will result in your using the operating system default Python
    which is likely not what you intend.

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
    The HPE Cray Python distribution provides Python 3. There is no Python 2
    version as Python 2 is now deprecated.
    
!!! tip
    The HPE Cray Python distribution is built using GCC compilers. If you wish
    to compile your own Python, C/C++ or Fortran code to use with HPE Cray
    Python, you should ensure that you compile using `PrgEnv-gnu` to make sure
    they are compatible.

## Adding your own packages

If the packages you require are not included in the HPE Cray Python
distribution, further packages can be added using `pip`. However, as the
/home file systems are not available on the compute nodes, you will need
to modify the default install location that `pip` uses to point to a
location on the /work file systems (by default, `pip` installs into
`$HOME/.local`). To do this, you set the `PYTHONUSERBASE` environment
variable to point to a location on /work, for example:

    export PYTHONUSERBASE=/work/t01/t01/auser/.local

You will also need to ensure that:

1. the location of commands installed by `pip` are available on the command
   line by modifying the `PATH` environment variable;
2. any packages you install are available to Python by modifying the 
   `PYTHONPATH` environment variable.
   
You can do this in the following way (once you have set `PYTHONUSERBASE` as described
above):

    export PATH=$PYTHONUSERBASE/bin:$PATH
    export PYTHONPATH=$PYTHONUSERBASE/lib/python3.8/site-packages:$PYTHONPATH

We would recommend adding all three of these commands to your `$HOME/.bashrc`
file to ensure they are set by default when you log in to ARCHER2.

Once, you have done this, you can use `pip` to add packages on top of the HPE 
Cray Python environment. This can be done using:

    module load cray-python
    pip install --user <package_name>

This uses the `--user` flag to ensure the packages are installed in
the directory specified by `PYTHONUSERBASE`.

We recommend that you use the `pipenv` and/or `virtualenv` packages to
manage your Python environments. For information on how to do this see:

   - [Pipenv and Virtual
     Environments](https://docs.python-guide.org/dev/virtualenvs/)

## Running Python on the compute nodes

In this section we provide example Python job submission scripts for a
variety of scenarios of using Python on the ARCHER2 compute nodes.

### Example serial Python submission script

=== "Full system"
    ```
    #!/bin/bash --login
    
    #SBATCH --job-name=python_test
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
    ```

=== "4-cabinet system"
    ```
    #!/bin/bash --login
    
    #SBATCH --job-name=python_test
    #SBATCH --nodes=1
    #SBATCH --tasks-per-node=1
    #SBATCH --cpus-per-task=1
    #SBATCH --time=00:10:00
    
    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard
    
    # Setup the batch environment
    module load epcc-job-env
    
    # Load the Python module
    module load cray-python
    
    # Run your Python progamme
    python python_test.py
    ```

!!! tip
    If you have installed your own packages you will need to set `PATH` and
    `PYTHONPATH` as described above within your job submission script in order
    to accesss the commands and packages you have installed.

### Example mpi4py job submission script

Programs that have been parallelised with mpi4py can be run on
multiple processors on ARCHER2. A sample submission script is given
below. The primary difference from the Python submission script in the
previous section is that we must run the program using
`srun python my_prog.py` instead of `python my_prog,py`. Failing to do so will 
cause a segmentation fault in your program when it reaches the line
"`from mpi4py import MPI`".

=== "Full system"
    ```
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
    ```

=== "4-cabinet system"
    ```
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
    
    # Setup the batch environment
    module load epcc-job-env
    
    # Load the Python module
    module load cray-python
    
    # Run your Python programme
    # Note that srun MUST be used to wrap the call to python, otherwise an error
    # will occur
    srun python mpi4py_test.py
    ```

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
   currently on (for a login node, this will be `uan01`, or similar; on a compute 
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

!!! note
    You willl of course need to use a different hostname for the SSH tunnel if you're running on the
    ARCHER2 4-cabinet system, `login-4c.archer2.ac.uk`.   

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
                       local_directory='$PWD'
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

