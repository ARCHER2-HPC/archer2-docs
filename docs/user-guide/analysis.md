# Data analysis

As well as being used for scientific simulations, ARCHER2 can also be
used for data pre-/post-processing and analysis. This page provides an
overview of the different options for doing so.

## Using the login nodes

The easiest way to run non-computationally intensive data analysis is to
run directly on the login nodes. However, please remember that the login
nodes are a shared resource and should not be used for long-running 
tasks.

#### Example: Running an R script on a login node

```bash
module load cray-R
Rscript example.R
```

## Using the compute nodes

If running on the login nodes is not feasible (e.g. due to memory
requirements or computationally intensive analysis), the compute nodes
can also be used for data analysis.

!!! important
    This is a more expensive option, as
    you will be charged for using the entire node, even though your analysis
    may only be using one core.

#### Example: Running an R script on a compute node

=== "Full system"
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

    module load cray-R

    Rscript example.R
    ```
=== "4-cabinet system"
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

=== "Full system"
    ```bash
    auser@ln01:> salloc --nodes=1 --tasks-per-node=1 --cpus-per-task=1 \
                  --time=00:20:00 --partition=standard --qos=short \
                  --account=[budget code]
    ```
=== "4-cabinet system"
    ```bash
    auser@uan01:> salloc --nodes=1 --tasks-per-node=1 --cpus-per-task=1 \
                  --time=00:20:00 --partition=standard --qos=short \
                  --reservation=shortqos --account=[budget code]
    ```

!!! note
    If you want to run for longer than 20 minutes, you will need to use
    a different QoS as the maximum runtime for the `short` QoS is 
    20 mins.

## Data analysis nodes

!!! important
    The data analysis nodes are only available on the full ARCHER2 
    system.

The data analysis nodes on the ARCHER2 full system are designed for large
compilations, post-calculation analysis and data manipulation. They should 
be used for jobs which are too small to require a whole compute node, but 
which would have an adverse impact on the operation of the login nodes if 
they were run interactively.

Unlike compute nodes, the data analysis nodes are able to access the home, 
work, and the RDFaaS file systems. They can also 
be used to transfer data from a remote system to ARCHER2 and vice versa 
(using e.g. `scp` or `rsync`). This can be useful when transferring large 
amounts of data that might take hours to complete.

### Requesting resources on the data analysis nodes using Slurm

The ARCHER2 data analysis nodes can be reached by using the `serial` 
partition and the `serial` QoS. Unlike other nodes on ARCHER2, you may 
only request part of a single node and you will likely be sharing the node 
with other users.

The data analysis nodes are set up such that you can specify the number of 
cores you want to use (up to 32 physical cores) and the amount of memory you 
want for your job (up to 125 GB). You can have multiple jobs running on the 
data analysis nodes at the same time, but the total number of cores used by 
those jobs cannot exceed 32, and the
total memory used by jobs currently running from a single user cannot exceed
125 GB -- any jobs above this limit will remain pending until your previous
jobs are finished.

You do not need to specify both number of cores *and* memory for jobs
on the data analysis nodes. By default, you will get 1984 MiB of
memory per core (which is a little less than 2 GB), when specifying
cores only, and 1 core when specifying the memory only.

!!! note
    Each data analysis node is fitted with 512 GB of memory. However,
    a small amount of this memory is needed for system processes,
    which is why we set an upper limit of 125 GB per user (a user is
    limited to one quarter of the RAM on a node). This is also why the
    per-core default memory allocation is slightly less than 2 GB.

!!! note
    When running on the data analysis nodes, you must always specify either 
    the number of cores you want, the amount of memory you want, or both. 
    The examples shown below specify the number of cores with the `--ntasks`
    flag and the memory with the `--mem` flag. If you are only wanting to 
    specify one of the two, please remember to delete the other one.

#### Example: Running a serial batch script on the data analysis nodes

A Slurm batch script for the data analysis nodes looks very similar to 
one for the compute nodes. The main differences are that you need to use 
`--partition=serial` and `--qos=serial`, specify the number of tasks
(rather than the number of nodes) and/or specify the amount of memory
you want. For example, to use a single core and 4 GB of memory, you 
would use something like:

```slurm
#!/bin/bash

# Slurm job options (job-name, job time)
#SBATCH --job-name=data_analysis
#SBATCH --time=0:20:0
#SBATCH --ntasks=1

# Replace [budget code] below with your budget code (e.g. t01)
#SBATCH --account=[budget code]             
#SBATCH --partition=serial
#SBATCH --qos=serial

# Define memory required for this jobs. By default, you would 
# get 2 GB, but you can ask for up to 128 GB.
#SBATCH --mem=4G

# Set the number of threads to 1
#   This prevents any threaded system libraries from automatically 
#   using threading.
export OMP_NUM_THREADS=1

module load cray-python

python my_analysis_script.py
```

### Interactive session on the data analysis nodes

There are two ways to start an interactive session on the data analysis nodes: 
you can either use `salloc` to reserve a part of a data analysis node for interactive 
jobs; or, you can use `srun` to open a terminal on the node and run things on the 
node directly. You can find out more information on the advantages and disadvantages 
of both of these methods in the [Running jobs on ARCHER2](scheduler.md#Interactive Jobs) 
section of the User and Best Practice Guide.

#### Using `salloc` for interactive access

You can reserve resources on a data analysis node using `salloc`. For
example, to request 1 core and 4 GB of memory for 20 minutes, you 
would use:

```bash 
auser@ln01:~> salloc --time=00:20:00 --partition=serial --qos=serial \
                    --account=[budget code] --ntasks=1 \
                    --mem=4G
```
    
When you submit this job, your terminal will display something like:

```bash
salloc: Pending job allocation 523113
salloc: job 523113 queued and waiting for resources
salloc: job 523113 has been allocated resources
salloc: Granted job allocation 523113
salloc: Waiting for resource configuration
salloc: Nodes dvn01 are ready for job

auser@ln01:~>
```
    
It may take some time for your interactive job to start. Once it runs
you will enter a standard interactive terminal session (a new shell).
Note that this shell is still on the front end (the prompt has not
changed). Whilst the interactive session lasts you will be able to run
jobs on the data analysis nodes by issuing the `srun` command directly at 
your command prompt. The maximum number of cores and memory you can use
is limited by resources requested in the `salloc` command (or by the 
defaults if you did not explicitly ask for particular amounts of resource).

Your session will end when you hit the requested walltime. If you wish
to finish before this you should use the `exit` command - this will
return you to your prompt before you issued the `salloc` command.

#### Using `srun` for interactive access

You can get a command prompt directly on the data analysis nodes by
using the `srun` command directly. For example, to reserve 1 core
and 8 GB of memory, you would use:

```bash 
auser@ln01:~> srun   --time=00:20:00 --partition=serial --qos=serial \
                    --account=[budget code]    \
                    --ntasks=1 --mem=8G \
                    --pty /bin/bash
```

The `--pty /bin/bash` will cause a new shell to be started on the data 
analysis node. (This is perhaps closer to what
many people consider an 'interactive' job than the method using the
`salloc` method described above.)

One can now issue shell commands in the usual way.

When finished, type `exit` to relinquish the allocation and control will
be returned to the front end.

By default, the interactive shell will retain the environment of the
parent. If you want a clean shell, remember to specify the `--export=none`
option to the `srun` command.

#### Visualising data using the data analysis nodes using X

You can view data on the data analysis nodes by starting an interactive 
`srun` session with the `--x11` flag to export the X display back to
your local system. For 1 core with * GB of memory:

```bash 
auser@ln01:~> srun   --time=00:20:00 --partition=serial --qos=serial  \
                        --hint=nomultithread --account=[budget code]    \
                        --ntasks=1 --mem=8G --x11 --pty /bin/bash
```
    

!!! tip
    Data visualisation on ARCHER2 is only possible if you used the `-X` 
    or `-Y` flag to the `ssh` command when when logging in to the system.

## Using Singularity

Singularity can be useful for data analysis, as sites such as
[DockerHub](http://hub.docker.com) or
[SingularityHub](https://singularity-hub.org/) contain many pre-built
images of data analysis tools that can be simply downloaded and used on
ARCHER2. More information about Singularity on ARCHER2 can be found
in the [Containers section](containers.md)
section of the User and Best Practice Guide.

## Data analysis tools

Useful tools for data analysis can be found on the
[Data Analysis and Tools](../data-tools/index.md) page.
