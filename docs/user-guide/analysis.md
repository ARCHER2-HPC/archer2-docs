# Data analysis

As well as being used for scientific simulations, ARCHER2 can also be
used for data pre-/post-processing and analysis. This page provides an
overview of the different options for doing so.

## Using the login nodes

The easiest way to run non-computationally intensive data analysis is to
run directly on the login nodes. However, please remember that the login
nodes are a shared resource. Any long-running scripts that make
intensive use of either the CPUs or the file system will cause slowdown
for other users.

#### Example: Running an R script on a login node

    ```bash
    module load cray-R
    Rscript example.R
    ```

## Using the compute nodes

If running on the login nodes is not feasible (e.g. due to memory
requirements or computationally intensive analysis), the compute nodes
can also be used for data analysis. This is a more expensive option, as
you will be charged for using the entire node, even though your analysis
may only be using one CPU.

#### Example: Running an R script on a compute node

    ```slurm
    #!/bin/bash
    #SBATCH --job-name=data_analysis
    #SBATCH --time=0:10:0
    #SBATCH --nodes=1
    #SBATCH --tasks-per-node=1
    #SBATCH --cpus-per-task=1

    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    module load epcc-job-env
    module load cray-R

    Rscript example.R
    ```

An advantage of this method is that you can use
[Job chaining](https://docs.archer2.ac.uk/user-guide/scheduler/#job-chaining)
to automate the process of analysing your output data once your compute
job has finished.

## Using interactive jobs

For more interactive analysis, it may be useful to use `salloc` to
reserve a compute node on which to do your analysis. This allows you to
run jobs directly on the compute nodes from the command line without
using a job submission script. More information on interactive jobs can
be found
[here](https://docs.archer2.ac.uk/user-guide/scheduler/#interactive-jobs).

#### Example: Reserving a single node for 20 minutes for interactive analysis

    ```bash
    auser@uan01:> salloc --nodes=1 --tasks-per-node=1 --cpus-per-task=1 \
                  --time=00:20:00 --partition=standard --qos=short \
                  --reservation=shortqos --account=[budget code]
    ```

## Data analysis nodes

The data analysis nodes on the ARCHER2 full system are designed for large
compilations, post-calculation analysis and data manipulation. They should 
be used for jobs which are too small to require a whole compute node, but 
which would have an adverse impact on the operation of the login nodes if 
they were run interactively.

Unlike compute nodes, the data analysis nodes are able to access the `/home`, 
`/work`, and `/rdfaas` directories. They can also be used to transfer data 
from a remote system to ARCHER2 and vice versa (using e.g. `scp` or `rsync`).
This can be useful when transferring large amounts of data that might take 
hours to complete.

The ARCHER2 data analysis nodes can be reached by using the `serial` 
partition and the `serial` queue. Unlike other nodes on ARCHER2, you may 
only request part of a single node and you will likely be sharing the node 
with other users.

The data analysis nodes are set up such that you can specify the number of 
cores you want to use (up to 32 physical cores) and the amount of memory you 
want for your job (up to 128 GB). You can have multiple jobs running on the 
data analysis nodes at the same time, but the total number of cores used by 
jobs currently running can not exceed 32 cores, and the total memory used 
by jobs currently running cannot exceed 128 GB -- any jobs above this limit 
will remain pending until your previous jobs are finished.

!!! note:
    When running on the data analysis nodes, you must always specify either 
    the number of cores you want, the amount of memory you want, or both. 
    The examples shown below specify the number of cores with the `--ntasks`
    flag and the memory with the `--mem` flag. If you are only wanting to 
    specify one of the two, please remember to delete the other one.

#### Example: Running an MPI batch script on the data analysis nodes

A Slurm batch script for the data analysis nodes looks very similar to 
one for the compute nodes. The main differences are that you need to use 
`--partition=serial` and `--qos=serial`:

    ```slurm
    #!/bin/bash

    # Slurm job options (job-name, compute nodes, job time)
    #SBATCH --job-name=Example_MPI_Job
    #SBATCH --time=0:20:0

    # Replace [budget code] below with your budget code (e.g. t01)
    #SBATCH --account=[budget code]             
    #SBATCH --partition=serial
    #SBATCH --qos=serial
    #SBATCH --hint=nomultithread
    
    # Define number of cores and memory for this jobs. You 
    # only need to define one of these two values, but should 
    # delete any you do not define.
    #SBATCH --ntasks=[number of cores]
    #SBATCH --mem=[memory in MB]

    # Set the number of threads to 1
    #   This prevents any threaded system libraries from automatically 
    #   using threading.
    export OMP_NUM_THREADS=1

    srun --distribution=block:block ./my_mpi_executable.x
    ```

### Interactive session on the data analysis nodes

There are two ways to start an interactive session on the data analysis nodes: 
you can either use `salloc` to reserve a part of a data analysis node for interactive 
jobs; or, you can use `srun` to open a terminal on the node and run things on the 
node directly. You can find out more information on the advantages and disadvantages 
of both of these methods in the [Running jobs on ARCHER2](#Interactive Jobs) 
part of the documentations.

#### Using `salloc` on the data analysis nodes

To reserve a part of a data analysis node using `salloc`, run:

    ```bash 
    auser@ln01:> salloc --time=00:20:00 --partition=serial --qos=serial    \
                        --account=[budget code] --ntasks=[number of cores] \
                        --mem=[memory in MB]
    ```
    
When you submit this job, your terminal will display something like:

    ```bash
    salloc: Pending job allocation 523113
    salloc: job 523113 queued and waiting for resources
    salloc: job 523113 has been allocated resources
    salloc: Granted job allocation 523113
    salloc: Waiting for resource configuration
    salloc: Nodes dvn01 are ready for job
    ````
    
It may take some time for your interactive job to start. Once it runs
you will enter a standard interactive terminal session (a new shell).
Note that this shell is still on the front end (the prompt has not
change). Whilst the interactive session lasts you will be able to run
parallel jobs on the compute nodes by issuing the `srun
--distribution=block:block --hint=nomultithread` command directly at 
your command prompt using the same syntax as you would inside a job
script. The maximum number of cores you can use is limited by resources
requested in the `salloc` command.

Your session will end when you hit the requested walltime. If you wish
to finish before this you should use the `exit` command - this will
return you to your prompt before you issued the `salloc` command.

#### Using `srun` on the data analysis nodes

To begin an interactive `srun` session on the data analysis nodes, run:

    ```bash 
    auser@ln01:> srun   --time=00:20:00 --partition=serial --qos=serial \
                        --hint=nomultithread --account=[budget code]    \
                        --ntasks=[number of cores] --mem=[memory in MB] \
                        --pty /bin/bash
    ```

The `--pty /bin/bash` will cause a new shell to be started on the first
node of a new allocation . This is perhaps closer to what
many people consider an 'interactive' job than the method using `salloc`
appears.

One can now issue shell commands in the usual way. A further invocation
of `srun` with the `--oversubscribe` flag is required to launch a parallel
job in the allocation.

When finished, type `exit` to relinquish the allocation and control will
be returned to the front end.

By default, the interactive shell will retain the environment of the
parent. If you want a clean shell, remember to specify `--export=none`.

#### Viewing data using the data analysis nodes

You can view data on the data analysis nodes by starting an interactive 
`srun` session with the `--x11` flag:

    ```bash 
    auser@ln01:> srun   --time=00:20:00 --partition=serial --qos=serial  \
                         --hint=nomultithread --account=[budget code]    \
                         --ntasks=[number of cores] --mem=[memory in MB] \
                         --x11 --pty /bin/bash
    ```
    
This will enable windows from the data analysis node to open on your local 
machine.

... note:
    Data visualisation on ARCHER2 is only possible if you used the `-XY` 
    flag when logging in to the system.

## Using Singularity

Singularity can be useful for data analysis, as sites such as
[DockerHub](http://hub.docker.com) or
[SingularityHub](https://singularity-hub.org/) contain many pre-built
images of data analysis tools that can be simply downloaded and used on
ARCHER2. More information about Singularity on ARCHER2 can be found
[here](https://docs.archer2.ac.uk/user-guide/containers).

## Data analysis tools

Useful tools for data analysis can be found on the
[Data Analysis and Tools](https://docs.archer2.ac.uk/data-tools/) page.
