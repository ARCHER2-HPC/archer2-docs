Running jobs on ARCHER2
=======================

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

As with most HPC services, ARCHER2 uses a scheduler to manage access to
resources and ensure that the thousands of different users of system
are able to share the system and all get access to the resources they
require. ARCHER2 uses the Slurm software to schedule jobs.

Writing a submission script is typically the most convenient way to
submit your job to the scheduler. Example submission scripts
(with explanations) for the most common job types are provided below.

Interactive jobs are also available and can be particularly useful for
developing and debugging applications. More details are available below.

.. hint::

  If you have any questions on how to run jobs on ARCHER2 do not hesitate
  to contact the `ARCHER2 Service Desk <mailto:support@archer2.ac.uk>`_.

You typically interact with Slurm by issuing Slurm commands
from the login nodes (to submit, check and cancel jobs), and by
specifying Slurm directives that describe the resources required for your
jobs in job submission scripts.


Basic Slurm commands
--------------------

There are three key commands used to interact with the Slurm on the
command line:

-  ``sinfo`` - Get information on the partitions and resources available
-  ``sbatch jobscript.slurm`` - Submit a job submission script (in this case called: ``jobscript.slurm``) to the scheduler
-  ``squeue`` - Get the current status of jobs submitted to the scheduler
-  ``scancel 12345`` - Cancel a job (in this case with the job ID ``12345``)

We cover each of these commands in more detail below.

``sinfo``: information on resources
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

``sinfo`` is used to query information about available resources and partitions.
Without any options, ``sinfo`` lists the status of all resources and partitions,
e.g.

.. TODO: Add example of sinfo command without options

::

  sinfo 

PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST 
standard     up 1-00:00:00    105  down* nid[001006,001017,001033,001045-001047,001061,001068,001070,001074,001109,001111,001113,001125,001138,001144,001149,001159-001160,001163,001171-001174,001203,001227-001228,001241-001244,001250-001255,001262,001273,001287,001326,001336,001347,001350-001351,001366,001369,001384-001395,001435,001462,001478,001490,001505,001539,001546,001552,001581,001614,001642-001645,001647,001652,001664,001669,001709,001719,001722-001723,001729,001747,001751,001757,001782,001810-001811,001813-001817,001836-001837,001839,001891-001892,001903,001919,001932,001944,001950,001955,002014] 
standard     up 1-00:00:00     12  drain nid[001016,001069,001092,001468,001520-001521,001812,001833-001835,001838,001969] 
standard     up 1-00:00:00      5   resv nid[001000,001002-001004,001114] 
standard     up 1-00:00:00    683  alloc nid[001001,001005,001007-001015,001018-001020,001024-001032,001034-001044,001048-001049,001051-001060,001062-001067,001071-001073,001075-001091,001093-001108,001110,001112,001115-001124,001126-001137,001139-001143,001145-001148,001150-001158,001161-001162,001164-001170,001176-001199,001246-001249,001256-001261,001288-001317,001319-001325,001327-001333,001339-001346,001348-001349,001367-001368,001416-001434,001436-001447,001463-001467,001469-001474,001501-001504,001512-001519,001522-001538,001540-001541,001543,001547-001551,001553-001580,001582-001613,001615-001639,001648-001651,001653-001663,001665-001668,001670-001688,001690-001707,001710-001718,001720-001721,001724-001728,001730-001746,001748-001750,001752-001756,001758-001781,001783-001809,001818-001824,001826-001831,001840-001890,001893-001902,001904-001918,001920-001931,001933-001942,001945-001949,001951-001954,001970-001991] 
standard     up 1-00:00:00    214   idle nid[001022-001023,001175,001200-001202,001204-001226,001229-001240,001245,001263-001272,001274-001286,001334-001335,001337-001338,001352-001365,001370-001383,001396-001415,001448-001461,001475-001477,001479-001489,001491-001500,001506-001511,001542,001544-001545,001640-001641,001646,001708,001832,001943,001956-001968,001992-002013,002015-002023] 
standard     up 1-00:00:00      2   down nid[001021,001050]

``sbatch``: submitting jobs
~~~~~~~~~~~~~~~~~~~~~~~~~~~

``sbatch`` is used to submit a job script to the job submission system. The script
will typically contain one or more ``srun`` commands to launch parallel tasks.

When you submit the job, the scheduler provides the job ID, which is used to identify
this job in other Slurm commands and when looking at resource usage in SAFE.

::

  sbatch test-job.slurm
  Submitted batch job 12345

``squeue``: monitoring jobs
~~~~~~~~~~~~~~~~~~~~~~~~~~~

``squeue`` without any options or arguments shows the current status of all jobs
known to the scheduler. For example:

::

  squeue

will list all jobs on ARCHER2.

The output of this is often overwhelmingly large. You can restrict the output
to just your jobs by adding the ``-u $USER`` option:

::

  squeue -u $USER

.. TODO: add example output

``scancel``: deleting jobs
~~~~~~~~~~~~~~~~~~~~~~~~~~

``scancel`` is used to delete a jobs from the scheduler. If the job is waiting 
to run it is simply cancelled, if it is a running job then it is stopped 
immediately. You need to provide the job ID of the job you wish to cancel/stop.
For example:

::

  scancel 12345

will cancel (if waiting) or stop (if running) the job with ID ``12345``.

Resource Limits
---------------

There are different resource limits on ARCHER2 for different purposes.

.. note::

   Details on the resource limits will be added when the ARCHER2 system
   is available.

.. TODO: Add in partition and QOS limits once they are known

Troubleshooting
---------------

Slurm error messages
~~~~~~~~~~~~~~~~~~~~

.. note::

  More information on common error messages will be added when the ARCHER2 system
  is available.

.. TODO: add in examples of common Slurm error messages

Slurm queued reasons
~~~~~~~~~~~~~~~~~~~~

.. note::

  Explanations of the reasons for jobs being queued and not running will be added
  when the ARCHER2 system is available.

.. TODO explain ``Reason`` column from ``squeue``

Output from Slurm jobs
----------------------

Slurm places standard output (STDOUT) and standard error (STDERR) for each
job in the file ``slurm_<JobID>.out``. This file appears in the
job's working directory once your job starts running.

.. note::

  This file is plain text and can contain useful information to help debugging
  if a job is not working as expected. The ARCHER2 Service Desk team will often
  ask you to provide the contents of this file if oyu contact them for help 
  with issues.

Specifying resources in job scripts
-----------------------------------

You specify the resources you require for your job using directives at the
top of your job submission script using lines that start with the directive
``#SBATCH``. 

.. note::

  Options provided using ``#SBATCH`` directives can also be specified as 
  command line options to ``srun``.

If you do not specify any options, then the default for each option will
be applied. As a minimum, all job submissions must specify the budget that
they wish to charge the job too with the option:

  - ``--account=<budgetID>`` your budget ID is usually something like
    ``t01`` or ``t01-test``. You can see which budget codes you can 
    charge to in SAFE.

Other common options that are used are:

  - ``--time=<hh:mm:ss>`` the maximum walltime for your job. *e.g.* For a 6.5 hour
    walltime, you would use ``--time=6:30:0``.
  - ``--job-name=<jobjob-name>`` set a job-name for the job to help identify it in 
    Slurm command output.

In addition, parallel jobs will also need to specify how many nodes,
parallel processes and threads they require.

  - ``--nodes=<nodes>`` the number of nodes to use for the job.
  - ``--tasks-per-node=<processes per node>`` the number of parallel processes
    (e.g. MPI ranks) per node.
  - ``--cpus-per-task=1`` if you are using parallel processes only with no
    threading then you should set the number of CPUs (cores) per parallel
    process to 1. **Note:** if you are using threading (e.g. with OpenMP)
    then you will need to change this option as described below.

For parallel jobs that use threading (e.g. OpenMP), you will also need to 
change the ``--cpus-per-task`` option.

  - ``--cpus-per-task=<threads per task>`` the number of threads per
    parallel process (e.g. number of OpenMP threads per MPI task for
    hybrid MPI/OpenMP jobs). **Note:** you must also set the ``OMP_NUM_THREADS``
    environment variable if using OpenMP in your job.

.. note::

  For parallel jobs, ARCHER2 operates in a *node exclusive* way. This means
  that you are assigned resources in the units of full compute nodes for your
  jobs (*i.e.* 128 cores) and that no other user can share those compute nodes
  with you. Hence, the minimum amount of resource you can request for a parallel
  job is 1 node (or 128 cores).

To prevent the behaviour of batch scripts being dependent on the user
environment at the point of submission, the option

  - ``--export=none`` prevents the user environment from being exported
    to the batch system.

Using the ``--export=none`` means that the behaviour of batch submissions
should be repeatable. We strongly recommend its use.


``srun``: Launching parallel jobs
---------------------------------

If you are running parallel jobs, your job submission script should contain
one or more ``srun`` commands to launch the parallel executable across the
compute nodes.

.. warning::

   To ensure that processes and threads are correctly mapped
   (or *pinned*) to cores, you should always specify `--cpu-bind=cores` option
   to `srun`.

Example job submission scripts
-------------------------------

A subset of example job submission scripts are included in full below. You 
can also download these examples at:

.. TODO: add links to job submission scripts

Using modules in the batch system
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Batch jobs must be submitted in the work file system ``/work`` as the
back end does not have access to the ``/home`` file system. This has
a knock-on effect on the behaviour of module collections, which the
module system expects to find in a users' home directory. In order
that the module system work correctly, batch scripts should contain

.. code-block:: console

  module restore /etc/cray-pe.d/PrgEnv-cray

to restore the default module collection before any other action.
This will also ensure all relevant library paths are set correctly
at run time. Note ``module -s`` can be used to suppress the associated
messages if desired.


Example: job submission script for MPI parallel job
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A simple MPI job submission script to submit a job using 4 compute
nodes and 128 MPI ranks per node for 20 minutes would look like:

::

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
    
    # Setup the job environment (this module needs to be loaded before any other modules)
    module load epcc-job-env
    
    # Set the number of threads to 1
    #   This prevents any threaded system libraries from automatically 
    #   using threading.
    export OMP_NUM_THREADS=1

    # Launch the parallel job
    #   Using 512 MPI processes and 128 MPI processes per node
    #   srun picks up the distribution from the sbatch options

    srun --cpu-bind=cores ./my_mpi_executable.x

This will run your executable "my\_mpi\_executable.x" in parallel on 512
MPI processes using 4 nodes (128 cores per node, i.e. not using hyper-threading). Slurm will
allocate 4 nodes to your job and srun will place 128 MPI processes on each node
(one per physical core).

See above for a more detailed discussion of the different ``sbatch`` options

Example: job submission script for MPI+OpenMP (mixed mode) parallel job
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. TODO: Update for ARCHER2

Mixed mode codes that use both MPI (or another distributed memory
parallel model) and OpenMP should take care to ensure that the shared
memory portion of the process/thread placement does not span more than
one node. This means that the number of shared memory threads should be
a factor of 128.

In the example below, we are using 4 nodes for 6 hours. There are 32 MPI
processes in total (8 MPI processes per node) and 16 OpenMP threads per MPI
process. This results in all 128 physical cores per node being used.

.. note:: 

   Note the use of the ``export OMP_PLACES=cores`` environment option and
   the ``--hint=nomultithread`` and ``--distribution=block:block``
   options to ``srun`` to generate the correct pinning.

::

  #!/bin/bash

  # Slurm job options (job-name, compute nodes, job time)
  #SBATCH --job-name=Example_MPI_Job
  #SBATCH --time=0:20:0
  #SBATCH --nodes=4
  #SBATCH --ntasks=32
  #SBATCH --tasks-per-node=8
  #SBATCH --cpus-per-task=16

  # Replace [budget code] below with your project code (e.g. t01)
  #SBATCH --account=[budget code] 
  #SBATCH --partition=standard
  #SBATCH --qos=standard
  
  # Setup the job environment (this module needs to be loaded before any other modules)
  module load epcc-job-env
  
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

Job arrays
----------

The Slurm job scheduling system offers the *job array* concept,
for running collections of almost-identical jobs. For example,
running the same program several times with different arguments
or input data.

Each job in a job array is called a *subjob*. The subjobs of a job
array can be submitted and queried as a unit, making it easier and
cleaner to handle the full set, compared to individual jobs.

All subjobs in a job array are started by running the same job script.
The job script also contains information on the number of jobs to be
started, and Slurm provides a subjob index which can be passed to
the individual subjobs or used to select the input data per subjob.

Job script for a job array
~~~~~~~~~~~~~~~~~~~~~~~~~~

As an example, the following script runs 56 subjobs, with the subjob
index as the only argument to the executable. Each subjob requests a
single node and uses all 128 cores on the node by placing 1 MPI 
process per core and specifies 4 hours maximum runtime per subjob:

::

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
    
    # Setup the job environment (this module needs to be loaded before any other modules)
    module load epcc-job-env
    
    # Set the number of threads to 1
    #   This prevents any threaded system libraries from automatically 
    #   using threading.
    export OMP_NUM_THREADS=1

    srun --cpu-bind=cores /path/to/exe $SLURM_ARRAY_TASK_ID


Submitting a job array
~~~~~~~~~~~~~~~~~~~~~~

Job arrays are submitted using ``sbatch`` in the same way as for standard
jobs:

::

    sbatch job_script.pbs

Job chaining
------------

Job dependencies can be used to construct complex pipelines or chain together long
simulations requiring multiple steps.

.. note::

   The ``--parsable`` option to ``sbatch`` can simplify working with job dependencies.
   It returns the job ID in a format that can be used as the input to other 
   commands.

For example:

::

   jobid=$(sbatch --parsable first_job.sh)
   sbatch --dependency=afterok:$jobid second_job.sh

or for a longer chain:

::

   jobid1=$(sbatch --parsable first_job.sh)
   jobid2=$(sbatch --parsable --dependency=afterok:$jobid1 second_job.sh)
   jobid3=$(sbatch --parsable --dependency=afterok:$jobid1 third_job.sh)
   sbatch --dependency=afterok:$jobid2,afterok:$jobid3 last_job.sh

Interactive Jobs: ``salloc``
----------------------------

When you are developing or debugging code you often want to run many
short jobs with a small amount of editing the code between runs. This
can be achieved by using the login nodes to run MPI but you may want
to test on the compute nodes (e.g. you may want to test running on 
multiple nodes across the high performance interconnect). One of the
best ways to achieve this on ARCHER2 is to use interactive jobs.

An interactive job allows you to issue ``srun`` commands directly
from the command line without using a job submission script, and to
see the output from your program directly in the terminal.

You use the ``salloc`` command to reserve compute nodes for interactive
jobs.

To submit a request for an interactive job reserving 8 nodes
(1024 physical cores) for 1 hour you would
issue the following qsub command from the command line:

::

    auser@uan01:> salloc --nodes=8 --tasks-per-node=128 --cpus-per-task=1 \
                  --time=01:00:00 --partition=standard --qos=standard \
                  --account=[budget code]
    
When you submit this job your terminal will display something like:

::

    salloc: Granted job allocation 24236
    salloc: Waiting for resource configuration
    salloc: Nodes nid000002 are ready for job
    auser@uan01:>

It may take some time for your interactive job to start. Once it
runs you will enter a standard interactive terminal session (a new shell).
Note that this shell is still on the front end (the prompt has not change).
Whilst the interactive session lasts you will be able to run parallel
jobs on the compute nodes by issuing the ``srun --cpu-bind=cores``  command
directly at your command prompt using the same syntax as you would inside
a job script. The maximum number of nodes you can use is limited by resources
requested in the ``salloc`` command.

If you know you will be doing a lot of intensive debugging you may
find it useful to request an interactive session lasting the expected
length of your working session, say a full day.

Your session will end when you hit the requested walltime. If you
wish to finish before this you should use the ``exit`` command - this will
return you to your prompt before you issued the ``salloc`` command.

Using ``srun`` directly
~~~~~~~~~~~~~~~~~~~~~~~

A second way to run an interactive job is to use ``srun`` directly in
the following way

::

  auser@uan01:>  srun --nodes=1 --exclusive --time=00:20:00 --account=[] \
                 --partition=standard --qos=standard --pty /bin/bash
  auser@uan01:> hostname
  nid001261

The ``--pty /bin/bash`` will cause a new shell to be started on the first
node of a new allocation (note that while the shell prompt has not
changed, we are now on the compute node). This is perhaps closer to
what many people consider an 'interactive' job than the method using
``salloc`` appears.

One can now issue shell commands in the usual way. A further invocation
of ``srun`` is required to launch a parallel job in the allocation.

When finished, type ``exit`` to relinquish the allocation and control
will be returned to the front end.

By default, the interactive shell will retain the environment of the
parent. If you want a clean shell, remember to specify ``--export=none``.

Reservations
------------

The mechanism for submitting reservations on ARCHER2 has yet to be specified.

.. TODO: Add information on how to submit reservations

Best practices for job submission
---------------------------------

This guidance is adapted from
`the advice provided by NERSC <https://docs.nersc.gov/jobs/best-practices/>`__

.. TODO: update to match ARCHER2

Do not run production jobs in /home
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. TODO: check - /home may not be available on compute nodes so this text may change

As a general best practice, users should run production runs from the
``/work`` file systems rather than the ``/home`` file systems.

The ``/home`` file system is designed for permanent and relatively small
storage. It is not tuned to perform well for parallel jobs and large amounts
of I/O. Home is perfect for storing files such as source codes and shell scripts.
Please note that while building software in /home is generally OK, it is best
to install dynamic libraries and binaries that are used on compute nodes
on the ``/work`` file systems for best performance.

The ``/work`` file systems are designed for large, temporary storage, particularly
for I/O from parallel jobs running on the compute nodes and large scale data analysis
(although the solid state storage may provide better performance in particular
scenarios). Running jobs on the ``/work`` file systems also helps
to improve the responsiveness of the ``/home`` file systems for all users.

Time Limits
~~~~~~~~~~~

Due to backfill scheduling, short and variable-length jobs generally
start quickly resulting in much better job throughput. You can specify a minimum
time for your job with the ``--time-min`` option to SBATCH:

::

    #SBATCH --time-min=<lower_bound>
    #SBATCH --time=<upper_bound>

Within your job script, you can get the time remaining in the job with
``squeue -h -j ${Slurm_JOBID} -o %L`` to allow you to deal with potentially
varying runtimes when using this option.

Long Running Jobs
~~~~~~~~~~~~~~~~~

Simulations which must run for a long period of time achieve the best
throughput when composed of many small jobs using a checkpoint and
restart method chained together (see above for how to chain jobs together).
However, this method does occur a startup and shutdown overhead for each
job as the state is saved and loaded so you should experiment to find the 
best balance between runtime (long runtimes minimise the checkpoint/restart
overheads) and throughput (short runtimes maximise throughput).

I/O performance
~~~~~~~~~~~~~~~

.. TODO: Advice on IO performance on ARCHER2

Large Jobs
~~~~~~~~~~

Large jobs may take longer to start up. The ``sbcast`` command 
is recommended for large jobs requesting over 1500 MPI tasks.
By default, Slurm reads the executable on the allocated compute nodes
from the location where it is installed; this may take long time when
the file system (where the executable resides) is slow or busy. The
``sbcast`` command, the executable can be copied to the ``/tmp``
directory on each of the compute nodes. Since ``/tmp`` is part of the
memory on the compute nodes, it can speed up the job startup time.

.. code:: bash

    sbcast --compress=lz4 /path/to/exe /tmp/exe
    srun /tmp/exe


Process Placement
~~~~~~~~~~~~~~~~~

Several mechanisms exist to control process placement on ARCHER2.
Application performance can depend strongly on placement
depending on the communication pattern and other computational
characteristics.

Default
^^^^^^^

The default is to place MPI tasks sequentially on nodes until the
maximum number of tasks is reached:

::

  salloc --nodes=8 --tasks-per-node=2 --cpus-per-task=1 --time=0:10:0 \
         --account=[account code] --partition=partition code] --qos=standard

  salloc: Granted job allocation 24236
  salloc: Waiting for resource configuration
  salloc: Nodes cn13 are ready for job

  module load xthi
  export OMP_NUM_THREADS=1
  srun --cpu-bind=cores xthi

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

``MPICH_RANK_REORDER_METHOD``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``MPICH_RANK_REORDER_METHOD`` environment variable is used to
specify other types of MPI task placement. For example, setting it to
0 results in a round-robin placement:

::

  salloc --nodes=8 --tasks-per-node=2 --cpus-per-task=1 --time=0:10:0 --account=t01

  salloc: Granted job allocation 24236
  salloc: Waiting for resource configuration
  salloc: Nodes cn13 are ready for job

  module load xthi
  export OMP_NUM_THREADS=1
  export MPICH_RANK_REORDER_METHOD=0
  srun --cpu-bind=cores xthi

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

There are other modes available with the ``MPICH_RANK_REORDER_METHOD``
environment variable, including one which lets the user provide a file
called ``MPICH_RANK_ORDER`` which contains a list of each task's
placement on each node. These options are described in detail in the
``intro_mpi`` man page.

**grid_order**

For MPI applications which perform a large amount of nearest-neighbor
communication, e.g., stencil-based applications on structured grids,
Cray provides a tool in the ``perftools-base`` module called
``grid_order`` which can generate a ``MPICH_RANK_ORDER`` file
automatically
by taking as parameters the dimensions of the grid, core count,
etc. For example, to place MPI tasks in row-major order on a Cartesian
grid of size $(4, 4, 4)$, using 32 tasks per node:

::

    module load perftools-base
    grid_order -R -c 32 -g 4,4,4

    # grid_order -R -Z -c 32 -g 4,4,4
    # Region 3: 0,0,1 (0..63)
    0,1,2,3,16,17,18,19,32,33,34,35,48,49,50,51,4,5,6,7,20,21,22,23,36,37,38,39,52,53,54,55
    8,9,10,11,24,25,26,27,40,41,42,43,56,57,58,59,12,13,14,15,28,29,30,31,44,45,46,47,60,61,62,63

One can then save this output to a file called ``MPICH_RANK_ORDER`` and
then set ``MPICH_RANK_REORDER_METHOD=3`` before running the job, which
tells Cray MPI to read the ``MPICH_RANK_ORDER`` file to set the MPI
task placement. For more information, please see the man page
``man grid_order`` (available when the ``perftools-base`` module is
loaded).

Huge pages
~~~~~~~~~~

Huge pages are virtual memory pages which are bigger than the default
page size of 4K bytes. Huge pages can improve memory performance
for common access patterns on large data sets since it helps to reduce
the number of virtual to physical address translations when compared
to using the default 4KB.

To use huge pages for an application (with the 2 MB huge pages as an
example):

::

    module load craype-hugepages2M
    cc -o mycode.exe mycode.c

And also load the same huge pages module at runtime.

.. warning::

  Due to the huge pages memory fragmentation issue, applications may get
  *Cannot allocate memory* warnings or errors when there are not enough
  hugepages on the compute node, such as: 
  ::
  
    libhugetlbfs [nid0000xx:xxxxx]: WARNING: New heap segment map at 0x10000000 failed: Cannot allocate memory``

By default, The verbosity level of libhugetlbfs ``HUGETLB_VERBOSE`` is set 
to ``0`` on ARCHER2 to surpress debugging messages. Users can adjust this value
to obtain more information on huge pages use.

When to Use Huge Pages
^^^^^^^^^^^^^^^^^^^^^^

-  For MPI applications, map the static data and/or heap onto huge
   pages.
-  For an application which uses shared memory, which needs to be
   concurrently registered with the high speed network drivers for
   remote communication.
-  For SHMEM applications, map the static data and/or private heap
   onto huge pages.
-  For applications written in Unified Parallel C, Coarray Fortran,
   and other languages based on the PGAS programming model, map the
   static data and/or private heap onto huge pages.
-  For an application doing heavy I/O.
-  To improve memory performance for common access patterns on large
   data sets.

When to Avoid Huge Pages
^^^^^^^^^^^^^^^^^^^^^^^^

-  Applications sometimes consist of many steering programs in addition
   to the core application. Applying huge page behavior to all processes
   would not provide any benefit and would consume huge pages that would
   otherwise benefit the core application. The runtime environment
   variable ``HUGETLB_RESTRICT_EXE`` can be used to specify the susbset of
   the programs to use hugepages.
-  For certain applications if using hugepages either causes issues or
   slows down performance. One such example is that when an application
   forks more subprocesses (such as pthreads) and these threads allocate
   memory, the newly allocated memory are the default 4 KB pages.



