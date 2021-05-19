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

    module load cray-R
    Rscript example.R

## Using the compute nodes

If running on the login nodes is not feasible (e.g. due to memory
requirements or computationally intensive analysis), the compute nodes
can also be used for data analysis. This is a more expensive option, as
you will be charged for using the entire node, even though your analysis
may only be using one CPU.

#### Example: Running an R script on a compute node

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

    auser@uan01:> salloc --nodes=1 --tasks-per-node=1 --cpus-per-task=1 \
                  --time=00:20:00 --partition=standard --qos=short \
                  --reservation=shortqos --account=[budget code]


## Data analysis nodes

The full ARCHER2 system will have dedicated data analysis nodes. Further
information on these nodes will be added here once it becomes available.

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
