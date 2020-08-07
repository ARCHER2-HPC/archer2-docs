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

  PARTITION       AVAIL  TIMELIMIT  NODES  STATE NODELIST
  standard           up 2-00:00:00      1  fail* cn580
  standard           up 2-00:00:00    128  down$ cn[96,579,793,814,1025-1044,1081-1088]
  standard           up 2-00:00:00     26  maint cn[27,93-95,206,232,310,492,568,577-578,585-588,813,815-816,818,846,889,921-924,956]
  standard           up 2-00:00:00      2   fail cn[274,871]
  standard           up 2-00:00:00      4  down* cn[528,614,637,845]
  standard           up 2-00:00:00   1034  alloc cn[1-26,28-38,40-58,62-86,88-92,97-174,176-205,207-231,233-273,275-309,311-333,335-341,344-371,373-376,378-413,415-452,454-489,493-513,515-527,529-532,535-539,541-550,554-561,563-567,569,572-576,581-584,589-595,598-601,603-613,615,617-620,623-631,633-636,638-647,651-659,661-678,680-687,690-695,697-716,718-736,738-775,777-790,792,794-812,817,819-844,847-852,854-870,872-888,890-920,925-955,957-977,980-1014,1016-1020,1023-1024,1045,1047-1070,1072-1080,1089-1105,1107-1152]
  standard           up 2-00:00:00     26   idle cn[61,490-491,540,551-552,562,570,596,602,621,632,648-650,660,688-689,696,853,978-979,1015,1021-1022,1071]

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

    # Set the number of threads to 1
    #   This prevents any threaded system libraries from automatically 
    #   using threading.
    export OMP_NUM_THREADS=1

    # Launch the parallel job
    #   Using 1024 MPI processes and 128 MPI processes per node
    #   srun picks up the distribution from the sbatch options
    srun --cpu-bind=cores ./my_mpi_executable.x

This will run your executable "my\_mpi\_executable.x" in parallel on 1024
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
   options to ``srun``to generate the correct pinning.

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
    #SBATCH --time=0:20:0
    #SBATCH --nodes=4
    #SBATCH --tasks-per-node=128
    #SBATCH --cpus-per-task=1
    #SBATCH --array=0-55

    # Replace [budget code] below with your budget code (e.g. t01)
    #SBATCH --account=[budget code]  

    # Set the number of threads to 1
    #   This prevents any threaded system libraries from automatically 
    #   using threading.
    export OMP_NUM_THREADS=1

    srun --cpu-bind=cores /path/to/exe $Slurm_ARRAY_TASK_ID


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

    salloc --nodes=8 --tasks-per-node=128 --cpus-per-task=1 --time=1:0:0 --account=[budget code]
    
When you submit this job your terminal will display something like:

::

    salloc: Granted job allocation 24236
    salloc: Waiting for resource configuration
    salloc: Nodes nid000002 are ready for job

It may take some time for your interactive job to start. Once it
runs you will enter a standard interactive terminal session.
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

Network Locality
~~~~~~~~~~~~~~~~

For jobs which are sensitive to interconnect (MPI) performance and
utilize less than or equal to 256 nodes it is possible to request that all nodes
are in a single Slingshot dragonfly group.

Slurm has a concept of "switches" which on ARCHER2 are configured to map
to Slingshot groups (there are 256 nodes per group). Since this places an additional constraint
on the scheduler a maximum time to wait for the requested topology can
be specified. For example:

::

    sbatch --switches=1@60 job.sh``

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

  salloc --nodes=8 --tasks-per-node=2 --cpus-per-task=1 --time=0:10:0 --account=t01

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



