# Running jobs on ARCHER2

As with most HPC services, ARCHER2 uses a scheduler to manage access to
resources and ensure that the thousands of different users of system are
able to share the system and all get access to the resources they
require. ARCHER2 uses the Slurm software to schedule jobs.

Writing a submission script is typically the most convenient way to
submit your job to the scheduler. Example submission scripts (with
explanations) for the most common job types are provided below.

Interactive jobs are also available and can be particularly useful for
developing and debugging applications. More details are available below.

!!! hint
    If you have any questions on how to run jobs on ARCHER2 do not hesitate
    to contact the [ARCHER2 Service Desk](mailto:support@archer2.ac.uk).

You typically interact with Slurm by issuing Slurm commands from the
login nodes (to submit, check and cancel jobs), and by specifying Slurm
directives that describe the resources required for your jobs in job
submission scripts.

## Resources

### CUs

Time used on ARCHER2 is measured in CUs.  
1 CU = 1 Node Hour for a standard 128 core node.

The [CU calculator](https://www.archer2.ac.uk/support-access/cu-calc.html) will help you to calculate the CU cost for your jobs.

### Checking available budget

You can check in [SAFE](https://safe.epcc.ed.ac.uk) by selecting `Login accounts` from the menu, select the login account you want to query.

Under `Login account details` you will see each of the budget codes you have access to listed e.g.
`e123 resources` and then under Resource Pool to the right of this, a note of the remaining budget in CUs. 

When logged in to the machine you can also use the command 

    sacctmgr show assoc where user=$LOGNAME format=account,user,maxtresmins

This will list all the budget codes that you have access to e.g.


       Account       User   MaxTRESMins
    ---------- ---------- -------------
          e123      userx         cpu=0
     e123-test      userx

This shows that `userx` is a member of budgets `e123` and `e123-test`.  However, the `cpu=0` indicates that the `e123` budget is empty or disabled.   This user can submit jobs using the `e123-test` budget.

To see the number of CUs remaining you must check in [SAFE](https://safe.epcc.ed.ac.uk).

### Charging

Jobs run on ARCHER2 are charged for the time they use i.e. from the time the job begins to run until the time the job ends (not the full wall time requested).

Jobs are charged for the full number of nodes which are requested, even if they are not all used.

Charging takes place at the time the job ends, and the job is charged in full to the budget which is live at the end time.


## Basic Slurm commands

There are four key commands used to interact with the Slurm on the
command line:

  - `sinfo` - Get information on the partitions and resources available
  - `sbatch jobscript.slurm` - Submit a job submission script (in this
    case called: `jobscript.slurm`) to the scheduler
  - `squeue` - Get the current status of jobs submitted to the scheduler
  - `scancel 12345` - Cancel a job (in this case with the job ID
    `12345`)

We cover each of these commands in more detail below.

### `sinfo`: information on resources

`sinfo` is used to query information about available resources and
partitions. Without any options, `sinfo` lists the status of all
resources and partitions, e.g.

```bash
auser@ln01:~> sinfo 

PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST 
standard     up 1-00:00:00    105  down* nid[001006,...,002014]
standard     up 1-00:00:00     12  drain nid[001016,...,001969]
standard     up 1-00:00:00      5   resv nid[001000,001002-001004,001114] 
standard     up 1-00:00:00    683  alloc nid[001001,...,001970-001991] 
standard     up 1-00:00:00    214   idle nid[001022-001023,...,002015-002023]
standard     up 1-00:00:00      2   down nid[001021,001050]
```

Here we see the number of nodes in different states. For example, 683
nodes are allocated (running jobs), and 214 are idle (available to run
jobs).

!!! note
    that long lists of node IDs have been abbreviated with `...`.

### `sbatch`: submitting jobs

`sbatch` is used to submit a job script to the job submission system.
The script will typically contain one or more `srun` commands to launch
parallel tasks.

When you submit the job, the scheduler provides the job ID, which is
used to identify this job in other Slurm commands and when looking at
resource usage in SAFE.

```bash
auser@ln01:~> sbatch test-job.slurm
Submitted batch job 12345
```

### `squeue`: monitoring jobs

`squeue` without any options or arguments shows the current status of
all jobs known to the scheduler. For example:

```bash
auser@ln01:~> squeue
```

will list all jobs on ARCHER2.

The output of this is often overwhelmingly large. You can restrict the
output to just your jobs by adding the `-u $USER` option:

```bash
auser@ln01:~> squeue -u $USER
```

### `scancel`: deleting jobs

`scancel` is used to delete a jobs from the scheduler. If the job is
waiting to run it is simply cancelled, if it is a running job then it is
stopped immediately.

If you only want to cancel a specific job you need to provide the job ID
of the job you wish to cancel/stop. For example:

```bash
auser@ln01:~> scancel 12345
```

will cancel (if waiting) or stop (if running) the job with ID `12345`.

`scancel` can take other options. For example, if you want to cancel all
your pending (queued) jobs but leave the running jobs running, you could
use:

```bash
auser@ln01:~> scancel --state=PENDING --user=$USER
```

## Resource Limits

The ARCHER2 resource limits for any given job are covered by three
separate attributes.

  - The amount of *primary resource* you require, i.e., number of
    compute nodes.
  - The *partition* that you want to use - this specifies the nodes that
    are eligible to run your job.
  - The *Quality of Service (QoS)* that you want to use - this specifies
    the job limits that apply.

### Primary resource

The *primary resource* you can request for your job is the compute node.

!!! information
    The `--exclusive` option is enforced on ARCHER2 which means you will
    always have access to all of the memory on the compute node regardless
    of how many processes are actually running on the node.

!!! note
    You will not generally have access to the full amount of memory resource
    on the the node as some is retained for running the operating system and
    other system processes.

### Partitions

On ARCHER2, compute nodes are grouped into partitions. You will have to
specify a partition using the `--partition` option in your Slurm
submission script. The following table has a list of active partitions
on ARCHER2.


=== "Full system"
    | Partition | Description                                                 | Max nodes available |
    | --------- | ----------------------------------------------------------- | ------------------- |
    | standard  | CPU nodes with AMD EPYC 7742 64-core processor &times; 2, 256/512 GB memory | 5860    |
    | highmem   | CPU nodes with AMD EPYC 7742 64-core processor &times; 2, 512 GB memory | 584     |
    | serial    | CPU nodes with AMD EPYC 7742 64-core processor &times; 2, 512 GB memory | 2       |

!!! note
    The `standard` partition includes both the standard memory and high memory nodes but standard memory
    nodes are preferentially chosen for jobs where possible. To guarantee access to high memory nodes
    you should specify the `highmem` partition.

### Quality of Service (QoS)

On ARCHER2, job limits are defined by the requested Quality of Service
(QoS), as specified by the `--qos` Slurm directive. The following table
lists the active QoS on ARCHER2.

=== "Full system"
    | QoS        | Max Nodes Per Job | Max Walltime | Jobs Queued | Jobs Running | Partition(s) | Notes |
    | ---------- | ----------------- | ------------ | ----------- | ------------ | ------------ | ------|
    | standard   | 1024               | 24 hrs       | 64          | 16           | standard     | Maximum of 1024 nodes in use by any one user at any time |
    | highmem   | 256               | 24 hrs       | 16          | 16           | highmem     | Maximum of 512 nodes in use by any one user at any time |
    | taskfarm   | 16               | 24 hrs       | 128          | 32           | standard     | Maximum of 256 nodes in use by any one user at any time |
    | short      | 32                 | 20 mins      | 16           | 4            | standard     | |
    | long       | 64                | 48 hrs       | 16          | 16           | standard     | Minimum walltime of 24 hrs, maximum 512 nodes in use by any one user at any time, maximum of 2048 nodes in use by QoS |
    | largescale | 5860               | 12 hrs        | 8           | 1            | standard     | Minimum job size of 1025 nodes |
    | lowpriority | 1024               | 24 hrs        | 16           | 16            | standard     | Jobs not charged but requires at least 1 CU in budget to use. |
    | serial | 32 cores and/or 128 GB memory   | 24 hrs        | 32           | 4            | serial    | Jobs not charged but requires at least 1 CU in budget to use. Maximum of 32 cores and/or 128 GB in use by any one user at any time. |
    | reservation | Size of reservation  | Length of reservation       | No limit           | no limit           | standard   |  |

You can find out the QoS that you can use by running the following
command:

=== "Full system"
   ```bash
   auser@ln01:~> sacctmgr show assoc user=$USER cluster=archer2 format=cluster,account,user,qos%50
   ```

!!! hint
    If you have needs which do not fit within the current QoS, please
    [contact the Service
    Desk](https://www.archer2.ac.uk/support-access/servicedesk.html) and we
    can discuss how to accommodate your requirements.

### E-mail notifications

E-mail notifications from the scheduler are not currently available
on ARCHER2.

### Priority

Job priority on ARCHER2 depends on a number of different factors:

 - The QoS your job has specified
 - The amount of time you have been queuing for
 - The number of nodes you have requested (job size)
 - Your current fairshare factor

Each of these factors is normalised to a value between 0 and 1, is multiplied
with a weight and the resulting values combined to produce a priority for the job. 
The current job priority formula on Tursa is:

```
Priority = [10000 * P(QoS)] + [500 * P(Age)] + [300 * P(Fairshare)] + [100 * P(size)]
```

The priority factors are:

- P(QoS) - The QoS priority normalised to a value between 0 and 1. The maximum raw
  value is 10000 and the minimum is 0. Most QoS on ARCHER2 have a raw prioity of 500; the
  `lowpriority` QoS has a raw priority of 1.
- P(Age) - The priority based on the job age normalised to a value between 0 and 1.
  The maximum raw value is 14 days (where P(Age) = 1).
- P(Fairshare) - The fairshare priority normalised to a value between 0 and 1. Your
  fairshare priority is determined by a combination of your budget code fairshare 
  value and your user fairshare value within that budget code. The more use that 
  the budget code you are using has made of the system recently relative to other 
  budget codes on the system, the lower the budget code fairshare value will be; and the more
  use you have made of the system recently relative to other users within your
  budget code, the lower your user fairshare value will be. The decay half life 
  for fairshare on ARCHER2 is set to 14 days. [More information on the Slurm fairshare
  algorithm](https://slurm.schedmd.com/fair_tree.html).
- P(Size) - The priority based on the job size normalised to a value between 0 and 1.
  The maximum size is the total number of ARCHER2 compute nodes.

You can view the priorities for current queued jobs on the system with the `sprio`
command:

```
auser@ln04:~> sprio -l
          JOBID PARTITION   PRIORITY       SITE        AGE  FAIRSHARE    JOBSIZE        QOS
         828764 standard        1049          0         45          0          4       1000
         828765 standard        1049          0         45          0          4       1000
         828770 standard        1049          0         45          0          4       1000
         828771 standard        1012          0          8          0          4       1000
         828773 standard        1012          0          8          0          4       1000
         828791 standard        1012          0          8          0          4       1000
         828797 standard        1118          0        115          0          4       1000
         828800 standard        1154          0        150          0          4       1000
         828801 standard        1154          0        150          0          4       1000
         828805 standard        1118          0        115          0          4       1000
         828806 standard        1154          0        150          0          4       1000
```

## Troubleshooting

### Slurm error messages

An incorrect submission will cause Slurm to return an error.
Some common problems are listed below, with a suggestion about
the likely cause:

* ``sbatch: unrecognized option <text>``
  
    One of your options is invalid or has a typo. ``man sbatch`` to help.


* ``error: Batch job submission failed: No partition specified or system default partition``
  
    A ``--partition=`` option is missing. You must specify the partition
    (see the list above). This is most often ``--partition=standard``.

* ``error: invalid partition specified: <partition>``

    ``error: Batch job submission failed: Invalid partition name specified``

    Check the partition exists and check the spelling is correct.


*  ``error: Batch job submission failed: Invalid account or account/partition combination specified``

    This probably means an invalid account has been given. Check the
    ``--account=`` options against valid accounts in SAFE.

* ``error: Batch job submission failed: Invalid qos specification``

    A QoS option is either missing or invalid. Check the script has a
    ``--qos=`` option and that the option is a valid one from the
    table above. (Check the spelling of the QoS is correct.)


* ``error: Your job has no time specification (--time=)...``

    Add an option of the form ``--time=hours:minutes:seconds`` to the
    submission script. E.g., ``--time=01:30:00`` gives a time limit of
    90 minutes.

* ``error: QOSMaxWallDurationPerJobLimit``
    ``error: Batch job submission failed: Job violates accounting/QOS policy``
    ``(job submit limit, user's size and/or time limits)``
  
    The script has probably specified a time limit which is too long for
    the corresponding QoS. E.g., the time limit for the short QoS
    is 20 minutes.


### Slurm job state codes

The ``squeue`` command allows users to view information for jobs managed by Slurm. Jobs
typically go through the following states: PENDING, RUNNING, COMPLETING, and COMPLETED.
The first table provides a description of some job state codes. The second table provides a description
of the [reasons](#slurm-queued-reasons) that cause a job to be in a state.


| Status        | Code | Description |
|---------------|------|-------------|
| PENDING       | PD   | Job is awaiting resource allocation. |
| RUNNING       | R    | Job currently has an allocation. |
| SUSPENDED     | S    | Job currently has an allocation. |
| COMPLETING    | CG   | Job is in the process of completing. Some processes on some nodes may still be active. |
| COMPLETED     | CD   | Job has terminated all processes on all nodes with an exit code of zero. |
| TIMEOUT       | TO   | Job terminated upon reaching its time limit. |
| STOPPED       | ST   | Job has an allocation, but execution has been stopped with SIGSTOP signal. CPUS have been retained by this job. |
| OUT_OF_MEMORY | OOM  | Job experienced out of memory error. |
| FAILED        | F    | Job terminated with non-zero exit code or other failure condition. |
| NODE_FAIL     | NF   | Job terminated due to failure of one or more allocated nodes. |
| CANCELLED     | CA   | Job was explicitly cancelled by the user or system administrator. The job may or may not have been initiated. |

For a full list of see [Job State Codes](https://slurm.schedmd.com/squeue.html#lbAG).

### Slurm queued reasons

| Reason | Description |
|--------|-------------|
| Priority | One or more higher priority jobs exist for this partition or advanced reservation. |
| Resources | The job is waiting for resources to become available. |
| BadConstraints | The job's constraints can not be satisfied. |
| BeginTime | The job's earliest start time has not yet been reached. |
| Dependency | This job is waiting for a dependent job to complete. |
| Licenses | The job is waiting for a license. |
| WaitingForScheduling | No reason has been set for this job yet. Waiting for the scheduler to determine the appropriate reason. |
| Prolog | Its PrologSlurmctld program is still running. |
| JobHeldAdmin | The job is held by a system administrator. |
| JobHeldUser | The job is held by the user. |
| JobLaunchFailure | The job could not be launched. This may be due to a file system problem, invalid program name, etc. |
| NonZeroExitCode | The job terminated with a non-zero exit code. |
| InvalidAccount | The job's account is invalid. |
| InvalidQOS | The job's QOS is invalid. |
| QOSUsageThreshold | Required QOS threshold has been breached. |
| QOSJobLimit | The job's QOS has reached its maximum job count. |
| QOSResourceLimit | The job's QOS has reached some resource limit. |
| QOSTimeLimit | The job's QOS has reached its time limit. |
| NodeDown | A node required by the job is down. |
| TimeLimit | The job exhausted its time limit. |
| ReqNodeNotAvail | Some node specifically required by the job is not currently available. The node may currently be in use, reserved for another job, in an advanced reservation, DOWN, DRAINED, or not responding. Nodes which are DOWN, DRAINED, or not responding will be identified as part of the job's "reason" field as "UnavailableNodes". Such nodes will typically require the intervention of a system administrator to make available. |

For a full list of see [Job Reasons](https://slurm.schedmd.com/squeue.html#lbAF).

## Output from Slurm jobs

Slurm places standard output (STDOUT) and standard error (STDERR) for
each job in the file `slurm_<JobID>.out`. This file appears in the job's
working directory once your job starts running.

!!! hint
    Output may be buffered - to enable live output, e.g. for monitoring
	job status, add `--unbuffered` to the `srun` command in your Slurm
	script.

## Specifying resources in job scripts

You specify the resources you require for your job using directives at
the top of your job submission script using lines that start with the
directive `#SBATCH`.

!!! hint
    Most options provided using `#SBATCH` directives can also be specified as
    command line options to `srun`.

If you do not specify any options, then the default for each option will
be applied. As a minimum, all job submissions must specify the budget
that they wish to charge the job too with the option:

   - `--account=<budgetID>` your budget ID is usually something like
     `t01` or `t01-test`. You can see which budget codes you can charge
     to in SAFE.

Other common options that are used are:

   - `--time=<hh:mm:ss>` the maximum walltime for your job. *e.g.* For
     a 6.5 hour walltime, you would use `--time=6:30:0`.
   - `--job-name=<jobname>` set a name for the job to help identify it
     in the queue

To prevent the behaviour of batch scripts being dependent on the user
environment at the point of submission, the option

   - `--export=none` prevents the user environment from being exported
     to the batch system.

Using the `--export=none` means that the behaviour of batch submissions
should be repeatable. We strongly recommend its use.

### Additional options for parallel jobs

In addition, parallel jobs will also need to specify how many nodes,
parallel processes and threads they require.

   - `--nodes=<nodes>` the number of nodes to use for the job.
   - `--tasks-per-node=<processes per node>` the number of parallel
     processes (e.g. MPI ranks) per node.
   - `--cpus-per-task=1` if you are using parallel processes only with
     no threading then you should set the number of CPUs (cores) per
     parallel process to 1. **Important:** if you are using threading (e.g.
     with OpenMP) then you will need to change this option as described
     below.

For parallel jobs that use threading (e.g. OpenMP), you will also need
to change the `--cpus-per-task` option.

   - `--cpus-per-task=<threads per task>` the number of threads per
     parallel process (e.g. number of OpenMP threads per MPI task for
     hybrid MPI/OpenMP jobs). **Important:** you must also set the
     `OMP_NUM_THREADS` environment variable if using OpenMP in your
     job.

!!! note
    For parallel jobs, ARCHER2 operates in a *node exclusive* way. This
    means that you are assigned resources in the units of full compute nodes
    for your jobs (*i.e.* 128 cores) and that no other user can share those
    compute nodes with you. Hence, the minimum amount of resource you can
    request for a parallel job is 1 node (or 128 cores).

### Options for jobs on the data analysis nodes

The data analysis nodes are shared between all users and can be used to 
run jobs that require small numbers of cores and/or access to an external
network to transfer data. These jobs are often **serial jobs** that only
require a single core.

To run jobs on the data analysis node you require the following options:

   - `--partition=serial` to select the data analysis nodes
   - `--qos=serial` to select the data analysis QoS (see above for QoS limits)
   - `--ntasks=<number of cores>` to select the number of cores you want
      to use in this job (up to the maximum defined in the QoS)
   - `--mem=<amount of memory>` to select the amount of memory you require
      (up to the maximum defined in the QoS).

More information on using the data analysis nodes (including example job
submission scripts) can be found in the
[Data Analysis section](analysis.md) of the User and Best Practice Guide.

## `srun`: Launching parallel jobs

If you are running parallel jobs, your job submission script should
contain one or more `srun` commands to launch the parallel executable
across the compute nodes. In most cases you will want to add the options
`--distribution=block:block` and `--hint=nomultithread` to your 
`srun` command to ensure you get the correct pinning of processes to 
cores on a compute node.

!!! warning
    If you do not add the `--distribution=block:block` and `--hint=nomultithread`
    options to your `srun` command the default process placement 
    may lead to a drop in performance for your jobs on ARCHER2.

A brief explanation of these options:
 - `--hint=nomultithread` - do not use hyperthreads/SMP
 - `--distribution=block:block` - the first `block` means use a block distribution
   of processes across nodes (i.e. fill nodes before moving onto the next one) and
   the second `block` means use a block distribution of processes across NUMA regions
   within a node (i.e. fill a NUMA region before moving on to the next one).

## bolt: Job submission script creation tool

The bolt job submission script creation tool has been written by EPCC to
simplify the process of writing job submission scripts for modern
multicore architectures. Based on the options you supply, bolt will
generate a job submission script that uses ARCHER2 in a reasonable way.

MPI, OpenMP and hybrid MPI/OpenMP jobs are supported.

!!! warning
    The tool will allow you to generate scripts for jobs that use the `long`
    QoS but you will need to manually modify the resulting script to change
    the QoS to `long`.

If there are problems or errors in your job parameter specifications
then bolt will print warnings or errors. However, bolt cannot detect all
problems.

### Basic Usage

The basic syntax for using bolt is:

    bolt -n [parallel tasks] -N [parallel tasks per node] -d [number of threads per task] \
         -t [wallclock time (h:m:s)] -o [script name] -j [job name] -A [project code]  [arguments...]

Example 1: to generate a job script to run an executable called
`my_prog.x` for 24 hours using 8192 parallel (MPI) processes and 128
(MPI) processes per compute node you would use something like:

    bolt -n 8192 -N 128 -t 24:0:0 -o my_job.bolt -j my_job -A z01-budget my_prog.x arg1 arg2

(remember to substitute `z01-budget` for your actual budget code.)

Example 2: to generate a job script to run an executable called
`my_prog.x` for 3 hours using 2048 parallel (MPI) processes and 64 (MPI)
processes per compute node (i.e. using half of the cores on a compute
node), you would use:

    bolt -n 2048 -N 64 -t 3:0:0 -o my_job.bolt -j my_job -A z01-budget my_prog.x arg1 arg2

These examples generate the job script `my_job.bolt` with the correct
options to run `my_prog.x` with command line arguments `arg1` and
`arg2`. The project code against which the job will be charged is
specified with the ' -A ' option. As usual, the job script is submitted
as follows:

    sbatch my_job.bolt

!!! hint
    If you do not specify the script name with the '-o' option then your
    script will be a file called `a.bolt`.

!!! hint
    If you do not specify the number of parallel tasks then bolt will try to
    generate a serial job submission script (and throw an error on the
    ARCHER2 4 cabinet system as serial jobs are not supported).

!!! hint
    If you do not specify a project code, bolt will use your default project
    code (set by your login account).

!!! hint
    If you do not specify a job name, bolt will use either `bolt_ser_job`
    (for serial jobs) or `bolt_par_job` (for parallel jobs).

### Further help

You can access further help on using bolt on ARCHER2 with the ' -h '
option:

    bolt -h

A selection of other useful options are:

   - `-s` Write and submit the job script rather than just writing the
     job script.
   - `-p` Force the job to be parallel even if it only uses a single
     parallel task.

## checkScript job submission script validation tool

The checkScript tool has been written to allow users to validate their
job submission scripts before submitting their jobs. The tool will read
your job submission script and try to identify errors, problems or
inconsistencies.

An example of the sort of output the tool can give would be:

    auser@ln01:/work/t01/t01/auser> checkScript submit.slurm 
    
    ===========================================================================
    checkScript
    ---------------------------------------------------------------------------
    Copyright 2011-2020  EPCC, The University of Edinburgh
    This program comes with ABSOLUTELY NO WARRANTY.
    This is free software, and you are welcome to redistribute it
    under certain conditions.
    ===========================================================================
    
    Script details
    ---------------
           User: auser
    Script file: submit.slurm
      Directory: /work/t01/t01/auser (ok)
       Job name: test (ok)
      Partition: standard (ok)
            QoS: standard (ok)
    Combination:          (ok)
    
    Requested resources
    -------------------
             nodes =              3                     (ok)
    tasks per node =             16
     cpus per task =              8
    cores per node =            128                     (ok)
    OpenMP defined =           True                     (ok)
          walltime =          1:0:0                     (ok)
    
    CU Usage Estimate (if full job time used)
    ------------------------------------------
                          CU =          3.000
    
    
    
    checkScript finished: 0 warning(s) and 0 error(s).

## Checking scripts and estimating start time with `--test-only`

`sbatch --test-only` validates the batch script and returns an estimate of when the job would be scheduled to run given the current job queue. Please note that it is just an estimate, the actual start time may differ as the job queue status when the start time was estimated may be different from the moment that the estimation took place. The job is not actually submitted.
    ```
    auser@ln01:~> sbatch --test-only submit.slurm
    sbatch: Job 1039497 to start at 2022-02-01T23:20:51 using 256 processors on nodes nid002836
    in partition standard
    ```

## Example job submission scripts

A subset of example job submission scripts are included in full below.
Examples are provided for both the full system and the 4-cabinet system.

### Example: job submission script for MPI parallel job

A simple MPI job submission script to submit a job using 4 compute nodes
and 128 MPI ranks per node for 20 minutes would look like:

=== "Full system"
    ```slurm
    #!/bin/bash

    # Slurm job options (job-name, compute nodes, job time)
    #SBATCH --job-name=Example_MPI_Job
    #SBATCH --time=0:20:0
    #SBATCH --nodes=4
    #SBATCH --tasks-per-node=128
    #SBATCH --cpus-per-task=1

    # Replace [budget code] below with your budget code (e.g. t01)
    #SBATCH --account=[budget code]             
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Set the number of threads to 1
    #   This prevents any threaded system libraries from automatically 
    #   using threading.
    export OMP_NUM_THREADS=1

    # Launch the parallel job
    #   Using 512 MPI processes and 128 MPI processes per node
    #   srun picks up the distribution from the sbatch options

    srun --distribution=block:block --hint=nomultithread ./my_mpi_executable.x
    ```

This will run your executable "my\_mpi\_executable.x" in parallel on 512
MPI processes using 4 nodes (128 cores per node, i.e. not using
hyper-threading). Slurm will allocate 4 nodes to your job and srun will
place 128 MPI processes on each node (one per physical core).

See above for a more detailed discussion of the different `sbatch`
options

### Example: job submission script for MPI+OpenMP (mixed mode) parallel job

Mixed mode codes that use both MPI (or another distributed memory
parallel model) and OpenMP should take care to ensure that the shared
memory portion of the process/thread placement does not span more than
one NUMA region. Nodes on ARCHER2 are made up of two sockets each
containing 4 NUMA regions of 16 cores, i.e. there are 8 NUMA regions in
total. Therefore the total number of threads should ideally not be
greater than 16, and also needs to be a factor of 16. Sensible choices
for the number of threads are therefore 1 (single-threaded), 2, 4, 8,
and 16. More information about using OpenMP and MPI+OpenMP can be found
in the Tuning chapter.

To ensure correct placement of MPI processes the number of cpus-per-task
needs to match the number of OpenMP threads, and the number of
tasks-per-node should be set to ensure the entire node is filled with
MPI tasks.

In the example below, we are using 4 nodes for 6 hours. There are 32 MPI
processes in total (8 MPI processes per node) and 16 OpenMP threads per
MPI process. This results in all 128 physical cores per node being used.

!!! hint
    Note the use of the `export OMP_PLACES=cores` environment option to
    generate the correct thread pinning.

=== "Full system"
    ```slurm
    #!/bin/bash

    # Slurm job options (job-name, compute nodes, job time)
    #SBATCH --job-name=Example_MPI_Job
    #SBATCH --time=0:20:0
    #SBATCH --nodes=4
    #SBATCH --tasks-per-node=8
    #SBATCH --cpus-per-task=16

    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code] 
    #SBATCH --partition=standard
    #SBATCH --qos=standard


    # Set the number of threads to 16 and specify placement
    #   There are 16 OpenMP threads per MPI process
    #   We want one thread per physical core
    export OMP_NUM_THREADS=16
    export OMP_PLACES=cores

    # Launch the parallel job
    #   Using 32 MPI processes
    #   8 MPI processes per node
    #   16 OpenMP threads per MPI process
    #   Additional srun options to pin one thread per physical core
    srun --hint=nomultithread --distribution=block:block ./my_mixed_executable.x arg1 arg2
    ```

The Slurm job scheduling system offers the *job array* concept, for
running collections of almost-identical jobs. For example, running the
same program several times with different arguments or input data.

Each job in a job array is called a *subjob*. The subjobs of a job array
can be submitted and queried as a unit, making it easier and cleaner to
handle the full set, compared to individual jobs.

All subjobs in a job array are started by running the same job script.
The job script also contains information on the number of jobs to be
started, and Slurm provides a subjob index which can be passed to the
individual subjobs or used to select the input data per subjob.

### Job script for a job array

As an example, the following script runs 56 subjobs, with the subjob
index as the only argument to the executable. Each subjob requests a
single node and uses all 128 cores on the node by placing 1 MPI process
per core and specifies 4 hours maximum runtime per subjob:

=== "Full system"
    ```slurm
    #!/bin/bash
    # Slurm job options (job-name, compute nodes, job time)
    #SBATCH --job-name=Example_Array_Job
    #SBATCH --time=04:00:00
    #SBATCH --nodes=1
    #SBATCH --tasks-per-node=128
    #SBATCH --cpus-per-task=1
    #SBATCH --array=0-55

    # Replace [budget code] below with your budget code (e.g. t01)
    #SBATCH --account=[budget code]  
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Set the number of threads to 1
    #   This prevents any threaded system libraries from automatically 
    #   using threading.
    export OMP_NUM_THREADS=1

    srun --distribution=block:block --hint=nomultithread /path/to/exe $SLURM_ARRAY_TASK_ID
    ```

### Submitting a job array

Job arrays are submitted using `sbatch` in the same way as for standard
jobs:

```
sbatch job_script.pbs 
```

## Job chaining

Job dependencies can be used to construct complex pipelines or chain
together long simulations requiring multiple steps.

!!! hint
    The `--parsable` option to `sbatch` can simplify working with job
    dependencies. It returns the job ID in a format that can be used as the
    input to other commands.

For example:

```
jobid=$(sbatch --parsable first_job.sh)
sbatch --dependency=afterok:$jobid second_job.sh
```

or for a longer chain:

```
jobid1=$(sbatch --parsable first_job.sh)
jobid2=$(sbatch --parsable --dependency=afterok:$jobid1 second_job.sh)
jobid3=$(sbatch --parsable --dependency=afterok:$jobid1 third_job.sh)
sbatch --dependency=afterok:$jobid2,afterok:$jobid3 last_job.sh
```

## Using multiple `srun` commands in a single job script

You can use multiple `srun` commands within in a Slurm job submission script
to allow you to use the resource requested more flexibly. For example, you 
could run a collection of smaller jobs within the requested resources or
you could even subdivide nodes if your individual calculations do not scale
up to use all 128 cores on a node.

In this guide we will cover two scenarios:

 1. Subdividing the job into multiple full-node or multi-node subjobs, e.g.
    requesting 100 nodes and running 100, 1-node subjobs or 50, 2-node 
    subjobs.
 2. Subdividing the job into multiple subjobs that each use a fraction of a
    node, e.g. requesting 2 nodes and running 256, 1-core subjobs or 16,
    16-core subjobs.

### Running multiple, full-node subjobs within a larger job

When subdivding a larger job into smaller subjobs you typically need to 
overwrite the `--nodes` option to `srun` and add the `--ntasks` option
to ensure that each subjob runs on the correct number of nodes and that
subjobs are placed correctly onto separate nodes.

For example, we will show how to request 100 nodes and then run 100
separate 1-node jobs, each of which use 128 MPI processes and which
run on a different compute node. We start by showing 
the job script that would achieve this and then explain how this works
and the options used. In our case, we will run 100 copies of the `xthi` 
program that prints the process placement on the node it is running on.

=== "Full system"
    ```slurm
    #!/bin/bash

    # Slurm job options (job-name, compute nodes, job time)
    #SBATCH --job-name=multi_xthi
    #SBATCH --time=0:20:0
    #SBATCH --nodes=100
    #SBATCH --tasks-per-node=128
    #SBATCH --cpus-per-task=1

    # Replace [budget code] below with your budget code (e.g. t01)
    #SBATCH --account=[budget code]             
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Load the xthi module
    module load xthi

    # Set the number of threads to 1
    #   This prevents any threaded system libraries from automatically 
    #   using threading.
    export OMP_NUM_THREADS=1

    # Loop over 100 subjobs starting each of them on a separate node
    for i in $(seq 1 100)
    do
    # Launch this subjob on 1 node, note nodes and ntasks options and & to place subjob in the background
        srun --nodes=1 --ntasks=128 --distribution=block:block --hint=nomultithread xthi > placement${i}.txt &
    done
    # Wait for all background subjobs to finish
    wait
    ```

Key points from the example job script:

- The `#SBATCH` options select 100 full nodes in the usual way.
- Each subjob `srun` command sets the following:
    - `--nodes=1` We need override this setting from the main job so that each subjob only uses 1 node
    - `--ntasks=128` For normal jobs, the number of parallel tasks (MPI processes) is calculated from
      the number of nodes you request and the number of tasks per node. We need to explicitly tell `srun`
      how many we require for this subjob.
    - `--distribution=block:block --hint=nomultithread` These options ensure correct placement of
      processes within the compute nodes.
    - `&` Each subjob `srun` command ends with an ampersand to place the process in the background
      and move on to the next loop iteration (and subjob submission). Without this, the script would
      wait for this subjob to complete before moving on to submit the next.
- Finally, there is the `wait` command to tell the script to wait for all the background subjobs
to complete before exiting. If we did not have this in place, the script would exit as soon as the
last subjob was submitted and kill all running subjobs.

### Running multiple subjobs that each use a fraction of a node

As the ARCHER2 nodes contain a large number of cores (128 per node) it
may sometimes be useful to be able to run multiple executables on a single
node. For example, you may want to run 128 copies of a serial executable or
Python script; or, you may want to run multiple copies of parallel executables
that use fewer than 128 cores each. This use model is possible using 
multiple `srun` commands in a job script on ARCHER2

!!! note
    You can never share a compute node with another user. Although you can
    use `srun` to place multiple copies of an executable or script on a 
    compute node, you still have exclusive use of that node. The minimum
    amount of resources you can reserve for your use on ARCHER2 is a single
    node.

When using `srun` to place multiple executables or scripts on a compute 
node you must be aware of a few things:

 - The `srun` command must specify any Slurm options that differ in value
   from those specified to `sbatch`. This typically means that you need 
   to specify the `--nodes`, `--ntasks` and `--tasks-per-node` options to `srun`.
 - On the ARCHER2 full system, you will need to include the `--oversubscribe` 
   flag to your `srun` command, and you will need to define the memory required 
   by each subjob with the `--mem=<amount of memory>` flag. The amount of memory 
   is given in MiB by default but other units can be specified. If you do not know 
   how much memory to specify, we recommend that you specify 1500M (1,500 MiB) per 
   core being used.
 - You will usually need to specify the task pinning to cores manually to 
   prevent multiple executables/scripts running on the same core. We provide
   a small utility (`genmaskcpu`) to assist with this. This utility is described
   below.
 - You will need to place each `srun` command into the background and 
   then use the `wait` command at the end of the submission script to
   make sure it does not exit before the commands are complete.
 - If you want to use more than one node in the job and use multiple `srun`
   per node (e.g. 256 single core processes across 2 nodes) then you need
   to pass the node ID to the `srun` commands otherwise Slurm will oversubscribe
   cores on the first node.

Below, we briefly describe the `genmaskcpu` helper script and then provide
three examples or running multiple subjobs in a node, one that runs 128
serial processes across a single node; one that runs 8 subjobs each of
which use 8 MPI processes with 2 OpenMP threads per MPI process; and one
that runs 256 serial processes across two nodes.

#### `genmaskcpu` helper script

When running multiple copies of executables/scripts on a single node Slurm
can often not pin processes or threads to cores in such a way to ensure
efficient use of resources (for example, multiple copies may end up bound
to the same core). For this reason, you often have to specify manual bindings
when running multiple executables/scripts on a node.

Writing CPU binding masks for use with Slurm by hand can be quite tedious,
especially if you want to use fat bitmasks to allow for binding of threads
to different cores. To assist with generating binding masks we provide a
utility script: `genmaskcpu`, that generates the correct bindings for 
each subjob based on a number of input arguments:

 1. Argument 1: The number of subjobs running *per node*
 2. Argument 2: The index of this subjob within the number running on a node (e.g. if
    there are 4 subjobs running per node, is this the 1st, 2nd, 3rd, or 4th
    subjob?)
 3. Argument 3: The number of parallel *processes* (MPI processes) per subjob (this must be the same
    for all subjobs)
 4. Argument 4: The number of *threads* per MPI process for the subjob (this must be 
    the same for all subjobs)

!!! important
    `genmaskcpu` only works for *homogeneous* subjobs, where every subjob
    on the node has the same number of parallel processes and threads per
    parallel process. If your subjobs do not have the same number of processes
    and threads then you will need to generate the binding masks manually.

For example, to generate the binding mask for the first subjob in a set of
8 subjobs on a node each of which uses 16 MPI processes and 1 thread per
MPI process, you would use:

```
mask=$(genmaskcpu 8 1 16 1)
```

The second subjob would use:

```
mask=$(genmaskcpu 8 2 16 1)
```

#### Example 1: 128 serial tasks running on a single node

For our first example, we will run 128 single-core copies of the `xthi` program (which
prints process/thread placement) on a single ARCHER2 compute node with each
copy of `xthi` pinned to a different core. We will use `genmaskcpu` to generate the 
correct binding mask for each subjob. The job submission script for
this example would look like:

=== "Full system"
    ```slurm
    #!/bin/bash
    # Slurm job options (job-name, compute nodes, job time)
    #SBATCH --job-name=MultiSerialOnCompute
    #SBATCH --time=0:10:0
    #SBATCH --nodes=1
    #SBATCH --tasks-per-node=128
    #SBATCH --cpus-per-task=1

    # Replace [budget code] below with your budget code (e.g. t01)
    #SBATCH --account=[budget code]  
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Make xthi available
    module load xthi

    # Make the pinning helper script available
    module load cray-python
    module load genmaskcpu

    # Set the number of threads to 1
    #   This prevents any threaded system libraries from automatically 
    #   using threading.
    export OMP_NUM_THREADS=1

    # Loop over 128 subjobs pinning each to a different core
    for i in $(seq 1 128)
    do
    # Generate mask: 128 subjobs per node, subjob number in sequence given by i,
    # 1 process per subjob, 1 thread per process
    maskcpu=$(genmaskcpu 128 ${i} 1 1)
    # Launch subjob overriding job settings as required and in the background
    # Make sure to change the amount specified by the `--mem=` flag to the amount 
    # of memory required. The amount of memory is given in MiB by default but other
    # units can be specified. If you do not know how much memory to specify, we 
    # recommend that you specify `--mem=1500M` (1,500 MiB).
    srun --cpu-bind=mask_cpu:${maskcpu} --nodes=1 --ntasks=1 --tasks-per-node=1 \
         --oversubscribe --mem=1500M xthi > placement${i}.txt &
    done

    # Wait for all subjobs to finish
    wait
    ```


#### Example 2: 8 subjobs on 1 node each with 8 MPI processes and 2 OpenMP threads per process

For our second example, we will run 8 subjobs, each running the `xthi` program (which
prints process/thread placement) across 1 node. Each subjob will use 8 MPI processes
and 2 OpenMP threads per process. We will use `genmaskcpu` to generate the 
correct binding mask for each subjob. The job submission script for
this example would look like:

=== "Full system"
    ```slurm
    #!/bin/bash
    # Slurm job options (job-name, compute nodes, job time)
    #SBATCH --job-name=MultiParallelOnCompute
    #SBATCH --time=0:10:0
    #SBATCH --nodes=1
    #SBATCH --tasks-per-node=64
    #SBATCH --cpus-per-task=2

    # Replace [budget code] below with your budget code (e.g. t01)
    #SBATCH --account=[budget code]  
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Make xthi available
    module load xthi

    # Make the pinning helper script available
    module load cray-python
    module load genmaskcpu

    # Set the number of threads to 2 as required by all subjobs
    export OMP_NUM_THREADS=2

    # Loop over 8 subjobs
    for i in $(seq 1 8)
    do
        echo $j $i
        # Generate mask: 8 subjobs per node, subjob number in sequence given by i,
        # 8 MPI processes per subjob, 2 OpenMP threads per process
        maskcpu=$(genmaskcpu 8 ${i} 8 2)
        # Launch subjob overriding job settings as required and in the background
        # Make sure to change the amount specified by the `--mem=` flag to the amount 
        # of memory required. The amount of memory is given in MiB by default but other
        # units can be specified. If you do not know how much memory to specify, we 
        # recommend that you specify `--mem=12500M` (12,500 MiB).
        srun --cpu-bind=mask_cpu:${maskcpu} --nodes=1 --ntasks=8 --tasks-per-node=8 --cpus-per-task=2 \
	     --oversubscribe --mem=12500M xthi > placement${i}.txt &
    done

    # Wait for all subjobs to finish
    wait
    ```

#### Example 3: 256 serial tasks running across two nodes

For our third example, we will run 256 single-core copies of the `xthi` program (which
prints process/thread placement) across two ARCHER2 compute nodes with each
copy of `xthi` pinned to a different core. We will use `genmaskcpu` to generate the 
correct binding mask for each subjob. We will also illustrate a mechnism for getting the
node IDs to pass to `srun` as this is required to ensure that the individual subjobs are
assigned to the correct node. This mechanism uses the `scontrol` command to turn the 
nodelist from `sbatch` into a format we can use as input to `srun`. The job submission
script for this example would look like:

=== "Full system"
    ```slurm
    #!/bin/bash
    # Slurm job options (job-name, compute nodes, job time)
    #SBATCH --job-name=MultiSerialOnComputes
    #SBATCH --time=0:10:0
    #SBATCH --nodes=2
    #SBATCH --tasks-per-node=128
    #SBATCH --cpus-per-task=1

    # Replace [budget code] below with your budget code (e.g. t01)
    #SBATCH --account=[budget code]  
    #SBATCH --partition=standard
    #SBATCH --qos=standard

    # Make xthi available
    module load xthi

    # Make the pinning helper script available
    module load cray-python
    module load genmaskcpu

    # Set the number of threads to 1
    #   This prevents any threaded system libraries from automatically 
    #   using threading.
    export OMP_NUM_THREADS=1

    # Get a list of the nodes assigned to this job in a format we can use.
    #   scontrol converts the condensed node IDs in the sbatch environment
    #   variable into a list of full node IDs that we can use with srun to
    #   ensure the subjobs are placed on the correct node. e.g. this converts
    #   "nid[001234,002345]" to "nid001234 nid002345"
    nodelist=$(scontrol show hostnames $SLURM_JOB_NODELIST)

    # Loop over the nodes assigned to the job
    for nodeid in $nodelist
    do
        # Loop over 128 subjobs on each node pinning each to a different core
        for i in $(seq 1 128)
        do
            # Generate mask: 128 subjobs per node, subjob number in sequence given by i,
            # 1 process per subjob, 1 thread per process
            maskcpu=$(genmaskcpu 128 ${i} 1 1)
            # Launch subjob overriding job settings as required and in the background
            # Make sure to change the amount specified by the `--mem=` flag to the amount 
            # of memory required. The amount of memory is given in MiB by default but other
            # units can be specified. If you do not know how much memory to specify, we 
            # recommend that you specify `--mem=1500M` (1,500 MiB).
            srun --cpu-bind=mask_cpu:${maskcpu} --nodelist=${nodeid} --nodes=1 --ntasks=1 --tasks-per-node=1 \
	         --oversubscribe --mem=1500M xthi > placement_${nodeid}_${i}.txt &
        done
    done

    # Wait for all subjobs to finish
    wait
    ```

## Interactive Jobs

### Using `salloc` to reserve resources

When you are developing or debugging code you often want to run many
short jobs with a small amount of editing the code between runs. This
can be achieved by using the login nodes to run MPI but you may want to
test on the compute nodes (e.g. you may want to test running on multiple
nodes across the high performance interconnect). One of the best ways to
achieve this on ARCHER2 is to use interactive jobs.

An interactive job allows you to issue `srun` commands directly from the
command line without using a job submission script, and to see the
output from your program directly in the terminal.

You use the `salloc` command to reserve compute nodes for interactive
jobs.

To submit a request for an interactive job reserving 8 nodes (1024
physical cores) for 20 minutes on the short queue you would issue the
following command from the command line:

=== "Full system"
    ```bash
    auser@ln01:> salloc --nodes=8 --tasks-per-node=128 --cpus-per-task=1 \
                    --time=00:20:00 --partition=standard --qos=short \
                    --account=[budget code]
    ```

When you submit this job your terminal will display something like:

=== "Full system"
    ```
    salloc: Granted job allocation 24236
    salloc: Waiting for resource configuration
    salloc: Nodes nid000002 are ready for job
    auser@ln01:>
    ```

It may take some time for your interactive job to start. Once it runs
you will enter a standard interactive terminal session (a new shell).
Note that this shell is still on the front end (the prompt has not
change). Whilst the interactive session lasts you will be able to run
parallel jobs on the compute nodes by issuing the `srun
--distribution=block:block --hint=nomultithread` command directly at 
your command prompt using the same syntax as you would inside a job
script. The maximum number of nodes you can use is limited by resources
requested in the `salloc` command.

If you know you will be doing a lot of intensive debugging you may find
it useful to request an interactive session lasting the expected length
of your working session, say a full day.

Your session will end when you hit the requested walltime. If you wish
to finish before this you should use the `exit` command - this will
return you to your prompt before you issued the `salloc` command.

### Using `srun` directly

A second way to run an interactive job is to use `srun` directly in the
following way (here using the "short queue"):

=== "Full system"
    ```
    auser@ln01:/work/t01/t01/auser> srun --nodes=1 --exclusive --time=00:20:00 \
                   --partition=standard --qos=short --reservation=shortqos \
		   --pty /bin/bash
    auser@nid001261:/work/t01/t01/auser> hostname
    nid001261
    ```
    
    The `--pty /bin/bash` will cause a new shell to be started on the first
    node of a new allocation . This is perhaps closer to what
    many people consider an 'interactive' job than the method using `salloc`
    appears.

    One can now issue shell commands in the usual way. A further invocation
    of `srun` is required to launch a parallel job in the allocation.
    
    !!! note
        When using `srun` within an interactive `srun` session, you will need to 
        include the `--oversubscribe` flag and specify the number of cores you want 
        to use:
        ```
        auser@nid001261:/work/t01/t01/auser> srun --oversubscribe --distribution=block:block \
                       --hint=nomultithread --ntasks=128 ./my_mpi_executable.x
        ```
    
    When finished, type `exit` to relinquish the allocation and control will
    be returned to the front end.
    
    By default, the interactive shell will retain the environment of the
    parent. If you want a clean shell, remember to specify `--export=none`.

## Heterogeneous jobs

The Slurm submissions discussed above involve a single executable image.
However, there are situtions where two or more distinct executables are
coupled and need to be run at the same time. This is most easily handled
via the Slurm heterogeneous job mechanism.

Two common cases are discussed below: first, a client server model in
which client and server each have a different `MPI_COMM_WORLD`, and second
the case were two or more executables share `MPI_COMM_WORLD`.


### Heterogeneous jobs for a client/server model: distinct `MPI_COMM_WORLDs`

The essential feature of a heterogeneous job here is to create a single batch
submission which specifies the resource requirements for the individual
components. Schematically, we would use
    
```
#!/bin/bash

# Slurm specifications for the first component

#SBATCH --partition=standard

...

#SBATCH hetjob

# Slurm specifications for the second component

#SBATCH --partition=standard

...

```
where new each component beyond the first is introduced by the special
token `#SBATCH hetjob` (note this is not a normal option and is not
`--hetjob`). Each component must specify a partition.

Such a job will appear in the queue system as, e.g.,
```
           50098+0  standard qscript-    user  PD       0:00      1 (None) 
           50098+1  standard qscript-    user  PD       0:00      2 (None) 
```
and counts as (in this case) two separate jobs from the point of
QoS limits.

Consider a case where we have two executables which may both be parallel (in
that they use MPI), both run at the same time, and communicate with each
other by some means other than MPI. In the following example, we run two
different executables, both of which must finish before the jobs completes.

```
#!/bin/bash

#SBATCH --time=00:20:00
#SBATCH --exclusive
#SBATCH --export=none

#SBATCH --partition=standard
#SBATCH --qos=standard

#SBATCH --nodes=1
#SBATCH --ntasks-per-node=8

#SBATCH hetjob

#SBATCH --partition=standard
#SBATCH --qos=standard

#SBATCH --nodes=2
#SBATCH --ntasks-per-node=4


# Run two execuatables with separate MPI_COMM_WORLD

srun --distribution=block:block --hint=nomultithread --het-group=0 ./xthi-a &
srun --distribution=block:block --hint=nomultithread --het-group=1 ./xthi-b &
wait
```
In this case, each executable is launched with a separate call to
`srun` but specifies a different heterogeneous group via the
`--het-group` option. The first group is `--het-group=0`.
Both are run in the background with `&` and the `wait` is required
to ensure both executables have completed before the job submission
exits.

In this rather artificial example, where each component makes a
simple report about its placement, the output might be
```
Node    0, hostname nid001028, mpi   4, omp   1, executable xthi-b
Node    1, hostname nid001048, mpi   4, omp   1, executable xthi-b
Node    0, rank    0, thread   0, (affinity =    0)
Node    0, rank    1, thread   0, (affinity =    1)
Node    0, rank    2, thread   0, (affinity =    2)
Node    0, rank    3, thread   0, (affinity =    3)
Node    1, rank    4, thread   0, (affinity =    0)
Node    1, rank    5, thread   0, (affinity =    1)
Node    1, rank    6, thread   0, (affinity =    2)
Node    1, rank    7, thread   0, (affinity =    3)
Node    0, hostname nid001027, mpi   8, omp   1, executable xthi-a
Node    0, rank    0, thread   0, (affinity =    0)
Node    0, rank    1, thread   0, (affinity =    1)
Node    0, rank    2, thread   0, (affinity =    2)
Node    0, rank    3, thread   0, (affinity =    3)
Node    0, rank    4, thread   0, (affinity =    4)
Node    0, rank    5, thread   0, (affinity =    5)
Node    0, rank    6, thread   0, (affinity =    6)
Node    0, rank    7, thread   0, (affinity =    7)
```
Here we have the first executable running on one node with
a communicator size 8 (ranks 0-7). The second executable runs on
two nodes also with communicator size 8 (ranks 0-7, 4 ranks per node).
Further examples of placement for heterogenenous jobs are given below.


### Heterogeneous jobs for a shared `MPI_COM_WORLD`

=== "Full system"
    !!! note
        The directive `SBATCH hetjob` can no longer be used for jobs requiring
        a shared `MPI_COMM_WORLD`
    
    If two or more heterogeneous components need to share a unique
    `MPI_COMM_WORLD`, a single `srun` invocation with the differrent
    components separated by a colon `:` should be used. Arguements
    to the individual components of the `srun` control the placement of
    the tasks and threads for each component. For example:
    
    ```
    #!/bin/bash
    
    #SBATCH --time=00:20:00
    #SBATCH --exclusive
    #SBATCH --export=none
    #SBATCH --account=[...]
    
    #SBATCH --partition=standard
    #SBATCH --qos=standard
    
    # We must specify correctly the total number of nodes required.
    #SBATCH --nodes=3
    
    SHARED_ARGS="--distribution=block:block --hint=nomultithread"
    
    srun --het-group=0 --nodes=1 --tasks-per-node=8 ${SHARED_ARGS} ./xthi-a : \
         --het-group=1 --nodes=2 --tasks-per-node=4 ${SHARED_ARGS} ./xthi-b
    ```
    
    The output should confirm we have a single `MPI_COMM_WORLD` with
    a total of three nodes, and ranks 0-15.
    ```
    Node summary for    3 nodes:
    Node    0, hostname nid002668, mpi   8, omp   1, executable xthi-a
    Node    1, hostname nid002669, mpi   4, omp   1, executable xthi-b
    Node    2, hostname nid002670, mpi   4, omp   1, executable xthi-b
    MPI summary: 16 ranks 
    Node    0, rank    0, thread   0, (affinity =    0) 
    Node    0, rank    1, thread   0, (affinity =    1) 
    Node    0, rank    2, thread   0, (affinity =    2) 
    Node    0, rank    3, thread   0, (affinity =    3) 
    Node    0, rank    4, thread   0, (affinity =    4) 
    Node    0, rank    5, thread   0, (affinity =    5) 
    Node    0, rank    6, thread   0, (affinity =    6) 
    Node    0, rank    7, thread   0, (affinity =    7) 
    Node    1, rank    8, thread   0, (affinity =    0) 
    Node    1, rank    9, thread   0, (affinity =    1) 
    Node    1, rank   10, thread   0, (affinity =    2) 
    Node    1, rank   11, thread   0, (affinity =    3) 
    Node    2, rank   12, thread   0, (affinity =    0) 
    Node    2, rank   13, thread   0, (affinity =    1) 
    Node    2, rank   14, thread   0, (affinity =    2) 
    Node    2, rank   15, thread   0, (affinity =    3) 
    
    ```

### Heterogeneous placement for mixed MPI/OpenMP work

Some care may be required for placement of tasks/threads in heterogeneous
jobs in which the number of threads needs to be specified differently
for different components.

In the following we have two components. The
first component runs 8 MPI tasks each with 16 OpenMP threads.
The second component runs 8 MPI tasks with
one task per NUMA region on one node; each task has one thread.
An appropriate Slurm submission might be:

=== "Full system"
       
    ```
    #!/bin/bash
    
    #SBATCH --time=00:20:00
    #SBATCH --exclusive
    #SBATCH --export=none
    #SBATCH --account=[...]

    #SBATCH --partition=standard
    #SBATCH --qos=standard
    
    #SBATCH --nodes=2

    SHARED_ARGS="--distribution=block:block --hint=nomultithread \
                 --nodes=1 --tasks-per-node=8 --cpus-per-task=16"

    # Do not set OMP_NUM_THREADS in the calling environment
    
    unset OMP_NUM_THREADS
    export OMP_PROC_BIND=spread

    srun --het-group=0 ${SHARED_ARGS} --export=all,OMP_NUM_THREADS=16 ./xthi-a : \
         --het-group=1 ${SHARED_ARGS} --export=all,OMP_NUM_THREADS=1  ./xthi-b
    
    ```
    
The important point here is that `OMP_NUM_THREADS` must not be set
in the environment that calls `srun` in order that the different
specifications for the separate groups via `--export` on the `srun`
command line take effect. If `OMP_NUM_THREADS` is set in the calling
environment, then that value takes precedence, and each component will
see the same value of `OMP_NUM_THREADS`.

The output would be:
```
Node    0, hostname nid001111, mpi   8, omp  16, executable xthi-a
Node    1, hostname nid001126, mpi   8, omp   1, executable xthi-b
Node    0, rank    0, thread   0, (affinity =    0)
Node    0, rank    0, thread   1, (affinity =    1)
Node    0, rank    0, thread   2, (affinity =    2)
Node    0, rank    0, thread   3, (affinity =    3)
Node    0, rank    0, thread   4, (affinity =    4)
Node    0, rank    0, thread   5, (affinity =    5)
Node    0, rank    0, thread   6, (affinity =    6)
Node    0, rank    0, thread   7, (affinity =    7)
Node    0, rank    0, thread   8, (affinity =    8)
Node    0, rank    0, thread   9, (affinity =    9)
Node    0, rank    0, thread  10, (affinity =   10)
Node    0, rank    0, thread  11, (affinity =   11)
Node    0, rank    0, thread  12, (affinity =   12)
Node    0, rank    0, thread  13, (affinity =   13)
Node    0, rank    0, thread  14, (affinity =   14)
Node    0, rank    0, thread  15, (affinity =   15)
Node    0, rank    1, thread   0, (affinity =   16)
Node    0, rank    1, thread   1, (affinity =   17)
...
Node    0, rank    7, thread  14, (affinity =  126)
Node    0, rank    7, thread  15, (affinity =  127)
Node    1, rank    8, thread   0, (affinity =    0)
Node    1, rank    9, thread   0, (affinity =   16)
Node    1, rank   10, thread   0, (affinity =   32)
Node    1, rank   11, thread   0, (affinity =   48)
Node    1, rank   12, thread   0, (affinity =   64)
Node    1, rank   13, thread   0, (affinity =   80)
Node    1, rank   14, thread   0, (affinity =   96)
Node    1, rank   15, thread   0, (affinity =  112)
```


## Low priority access

Low priority jobs are not charged against your allocation but will only run when
other, higher-priority, jobs cannot be run or there are no higher-priority jobs in
the queue. Although low priority jobs are not charged, you do need a valid, positive
budget to be able to submit and run low priority jobs, i.e. you need at least 1 CU
in your budget.

Low priority access is always available and has the following limits:

=== "Full system"
    - 1024 node maximum job size
    - Maximum 16 low priority jobs in the queue per user
    - Maximum 16 low priority job running per user (of the 16 queued)
    - Maximum runtime of 24 hours

You submit a low priority job on ARCHER2 by using the `lowpriority` QoS. For example,
you would usually have the following line in your job submission script sbatch 
options:

```
#SBATCH --qos=lowpriority
```

## Reservations

Reservations are available on ARCHER2. These allow users to reserve a number of nodes
for a specified length of time starting at a particular time on the system.

Reservations require justification. They will only be approved if the request could not
be fulfilled with the standard queues. For instance, you require a job/jobs to run at a
particular time e.g. for a demonstration or course.

!!! note
    Reservation requests must be submitted at least 60 hours in advance of the reservation
    start time. If requesting a reservation for a Monday at 18:00, please ensure this is
    received by the Friday at 12:00 the latest. The same applies over Service Holidays.

!!! note
    Reservations are only valid for standard compute nodes, high memory compute nodes
    and/or PP nodes cannot be included in reservations.

Reservations will be charged at 1.5 times the usual CU rate and our policy is that they
will be charged the full rate for the entire reservation at the time of booking, whether
or not you use the nodes for the full time. In addition, you will not be refunded the
CUs if you fail to use them due to a job crash unless this crash is due to a system failure.

!!! bug
    At the moment, we are only able to charge for jobs in reservations, not for the full 
    reservation itself. Jobs in reservations are charged at 1.5x the standard rate.

To request a reservation please [contact the ARCHER2 Service Desk](mailto:support@archer2.ac.uk).
You need to provide the following:

 - The start time and date of the reservation.
 - The end time and date of the reservation.
 - The project code for the reservation.
 - The number of nodes required.
 - Your justification for the reservation -- this must be provided or the request will be rejected.

Your request will be checked by the ARCHER2 User Administration team and, if approved, you will be provided a reservation ID which can be used on the system. To submit jobs to a reservation, you need to add `--reservation=<reservation ID>` and `--qos=reservation` options to your job submission script or command.

!!! important
    You must have at least 1 CU in the budget to submit a job on ARCHER2, even to a pre-paid reservation.

!!! tip
    You can submit jobs to a reservation as soon as the reservation has been set up; jobs will remain queued until the reservation starts.

## Serial jobs

You can run serial jobs on the shared data analysis nodes. More information
on using the data analysis nodes (including example job submission scripts)
can be found in the [Data Analysis section](analysis.md) of the User and Best
Practice Guide.

## Best practices for job submission

This guidance is adapted from [the advice provided by
NERSC](https://docs.nersc.gov/jobs/best-practices/)

### Time Limits

Due to backfill scheduling, short and variable-length jobs generally
start quickly resulting in much better job throughput. You can specify a
minimum time for your job with the `--time-min` option to SBATCH:

    #SBATCH --time-min=<lower_bound>
    #SBATCH --time=<upper_bound>

Within your job script, you can get the time remaining in the job with
`squeue -h -j ${Slurm_JOBID} -o %L` to allow you to deal with
potentially varying runtimes when using this option.

### Long Running Jobs

Simulations which must run for a long period of time achieve the best
throughput when composed of many small jobs using a checkpoint and
restart method chained together (see above for how to chain jobs
together). However, this method does occur a startup and shutdown
overhead for each job as the state is saved and loaded so you should
experiment to find the best balance between runtime (long runtimes
minimise the checkpoint/restart overheads) and throughput (short
runtimes maximise throughput).

### Interconnect locality

!!! important
    The locality options are only available on the full ARCHER2 
    system. The 4-cabinet system does not support this option.

For jobs which are sensitive to interconnect (MPI) performance and
utilise 128 nodes or less it is possible to request that all nodes
are in a single Slingshot dragonfly group. The maximum number of nodes in
a group on ARCHER2 is 128.

Slurm has a concept of "switches" which on ARCHER2 are configured to map
to Slingshot electrical groups; where all compute nodes have all-to-all
electrical connections which minimises latency. Since this places an
additional constraint on the scheduler a maximum time to wait for the
requested topology can be specified - after this time, the job will be
placed without the constraint.

For example, to specify that all requested nodes should come from one
electrical group and to wait for up to 6 hours (360 minutes) for that
placement, you would use the following option in your job:

```slurm
#SBATCH --switches=1@360
```

You can request multiple groups using this option if you are using 
more nodes than are in a single group to maximise the number of
nodes that share electrical connetions in the job. For example, to
request 4 groups (maximum of 512 nodes) and have this as an absolute
constraint with no timeout, you would use:

```slurm
#SBATCH --switches=4
```

!!! danger
    When specifying the number of groups take care to request enough
    groups to satisfy the requested number of nodes. If the number
    is too low then an unneccesary delay will be added due to the
    unsatisfiable request.

    A useful heuristic to ensure this is the case is to ensure that
    the total nodes requested is greater than or equal to the number
    of groups multiplied by 128.


### Large Jobs

Large jobs may take longer to start up. The `sbcast` command is
recommended for large jobs requesting over 1500 MPI tasks. By default,
Slurm reads the executable on the allocated compute nodes from the
location where it is installed; this may take long time when the file
system (where the executable resides) is slow or busy. The `sbcast`
command, the executable can be copied to the `/tmp` directory on each of
the compute nodes. Since `/tmp` is part of the memory on the compute
nodes, it can speed up the job startup time.

```
sbcast --compress=lz4 /path/to/exe /tmp/exe
srun /tmp/exe
```

### Process Placement

Several mechanisms exist to control process placement on ARCHER2.
Application performance can depend strongly on placement depending on
the communication pattern and other computational characteristics.

#### Default

The default is to place MPI tasks sequentially on nodes until the
maximum number of tasks is reached:

```
salloc --nodes=8 --tasks-per-node=2 --cpus-per-task=1 --time=0:10:0 \
        --account=[account code] --partition=standard --qos=standard

salloc: Granted job allocation 24236
salloc: Waiting for resource configuration
salloc: Nodes cn13 are ready for job

module load xthi
export OMP_NUM_THREADS=1
srun --distribution=block:block --hint=nomultithread xthi

Hello from rank 0, thread 0, on nid000001. (core affinity = 0,128)
Hello from rank 1, thread 0, on nid000001. (core affinity = 16,144)
Hello from rank 2, thread 0, on nid000002. (core affinity = 0,128)
Hello from rank 3, thread 0, on nid000002. (core affinity = 16,144)
Hello from rank 4, thread 0, on nid000003. (core affinity = 0,128)
Hello from rank 5, thread 0, on nid000003. (core affinity = 16,144)
Hello from rank 6, thread 0, on nid000004. (core affinity = 0,128)
Hello from rank 7, thread 0, on nid000004. (core affinity = 16,144)
Hello from rank 8, thread 0, on nid000005. (core affinity = 0,128)
Hello from rank 9, thread 0, on nid000005. (core affinity = 16,144)
Hello from rank 10, thread 0, on nid000006. (core affinity = 0,128)
Hello from rank 11, thread 0, on nid000006. (core affinity = 16,144)
Hello from rank 12, thread 0, on nid000007. (core affinity = 0,128)
Hello from rank 13, thread 0, on nid000007. (core affinity = 16,144)
Hello from rank 14, thread 0, on nid000008. (core affinity = 0,128)
Hello from rank 15, thread 0, on nid000008. (core affinity = 16,144)
```

#### `MPICH_RANK_REORDER_METHOD`

The `MPICH_RANK_REORDER_METHOD` environment variable is used to specify
other types of MPI task placement. For example, setting it to 0 results
in a round-robin placement:

```
salloc --nodes=8 --tasks-per-node=2 --cpus-per-task=1 --time=0:10:0 --account=t01

salloc: Granted job allocation 24236
salloc: Waiting for resource configuration
salloc: Nodes cn13 are ready for job

module load xthi
export OMP_NUM_THREADS=1
export MPICH_RANK_REORDER_METHOD=0
srun xthi

Hello from rank 0, thread 0, on nid000001. (core affinity = 0,128)
Hello from rank 1, thread 0, on nid000002. (core affinity = 0,128)
Hello from rank 2, thread 0, on nid000003. (core affinity = 0,128)
Hello from rank 3, thread 0, on nid000004. (core affinity = 0,128)
Hello from rank 4, thread 0, on nid000005. (core affinity = 0,128)
Hello from rank 5, thread 0, on nid000006. (core affinity = 0,128)
Hello from rank 6, thread 0, on nid000007. (core affinity = 0,128)
Hello from rank 7, thread 0, on nid000008. (core affinity = 0,128)
Hello from rank 8, thread 0, on nid000001. (core affinity = 16,144)
Hello from rank 9, thread 0, on nid000002. (core affinity = 16,144)
Hello from rank 10, thread 0, on nid000003. (core affinity = 16,144)
Hello from rank 11, thread 0, on nid000004. (core affinity = 16,144)
Hello from rank 12, thread 0, on nid000005. (core affinity = 16,144)
Hello from rank 13, thread 0, on nid000006. (core affinity = 16,144)
Hello from rank 14, thread 0, on nid000007. (core affinity = 16,144)
Hello from rank 15, thread 0, on nid000008. (core affinity = 16,144)
```

There are other modes available with the `MPICH_RANK_REORDER_METHOD`
environment variable, including one which lets the user provide a file
called `MPICH_RANK_ORDER` which contains a list of each task's placement
on each node. These options are described in detail in the `intro_mpi`
man page.

**grid\_order**

For MPI applications which perform a large amount of nearest-neighbor
communication, e.g., stencil-based applications on structured grids,
Cray provides a tool in the `perftools-base` module called `grid_order`
which can generate a `MPICH_RANK_ORDER` file automatically by taking as
parameters the dimensions of the grid, core count, etc. For example, to
place MPI tasks in row-major order on a Cartesian grid of size $(4, 4,
4)$, using 32 tasks per node:

```
module load perftools-base
grid_order -R -c 32 -g 4,4,4

# grid_order -R -Z -c 32 -g 4,4,4
# Region 3: 0,0,1 (0..63)
0,1,2,3,16,17,18,19,32,33,34,35,48,49,50,51,4,5,6,7,20,21,22,23,36,37,38,39,52,53,54,55
8,9,10,11,24,25,26,27,40,41,42,43,56,57,58,59,12,13,14,15,28,29,30,31,44,45,46,47,60,61,62,63
```

One can then save this output to a file called `MPICH_RANK_ORDER` and
then set `MPICH_RANK_REORDER_METHOD=3` before running the job, which
tells Cray MPI to read the `MPICH_RANK_ORDER` file to set the MPI task
placement. For more information, please see the man page `man
grid_order` (available when the `perftools-base` module is loaded).

### Huge pages

Huge pages are virtual memory pages which are bigger than the default
page size of 4K bytes. Huge pages can improve memory performance for
common access patterns on large data sets since it helps to reduce the
number of virtual to physical address translations when compared to
using the default 4KB.

To use huge pages for an application (with the 2 MB huge pages as an
example):

```
module load craype-hugepages2M
cc -o mycode.exe mycode.c
```

And also load the same huge pages module at runtime.

!!! warning
    Due to the huge pages memory fragmentation issue, applications may get
    *Cannot allocate memory* warnings or errors when there are not enough
    hugepages on the compute node, such as:

    libhugetlbfs [nid0000xx:xxxxx]: WARNING: New heap segment map at 0x10000000 failed: Cannot allocate memory``

By default, The verbosity level of libhugetlbfs `HUGETLB_VERBOSE` is set
to `0` on ARCHER2 to surpress debugging messages. Users can adjust this
value to obtain more information on huge pages use.

#### When to Use Huge Pages

  - For MPI applications, map the static data and/or heap onto huge
    pages.
  - For an application which uses shared memory, which needs to be
    concurrently registered with the high speed network drivers for
    remote communication.
  - For SHMEM applications, map the static data and/or private heap onto
    huge pages.
  - For applications written in Unified Parallel C, Coarray Fortran, and
    other languages based on the PGAS programming model, map the static
    data and/or private heap onto huge pages.
  - For an application doing heavy I/O.
  - To improve memory performance for common access patterns on large
    data sets.

#### When to Avoid Huge Pages

  - Applications sometimes consist of many steering programs in addition
    to the core application. Applying huge page behavior to all
    processes would not provide any benefit and would consume huge pages
    that would otherwise benefit the core application. The runtime
    environment variable `HUGETLB_RESTRICT_EXE` can be used to specify
    the susbset of the programs to use hugepages.
  - For certain applications if using hugepages either causes issues or
    slows down performance. One such example is that when an application
    forks more subprocesses (such as pthreads) and these threads
    allocate memory, the newly allocated memory are the default 4 KB
    pages.
