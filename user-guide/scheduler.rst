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

.. info::

  If you have any questions on how to run jobs on Cirrus do not hesitate
  to contact the `ARCHER2 Service Desk <mailto:support@archer2.ac.uk>`_.

You typically interact with Slurm by issuing Slurm commands
from the login nodes (to submit, check and cancel jobs), and by
specifying Slurm directives that describe the resources required for your
jobs in job submission scripts.

Best practice
-------------

This guidance is adapted from
`the advice provided by ARCHER2 <https://docs.nersc.gov/jobs/best-practices/>`__

.. TODO: update to match ARCHER2

Do not run production jobs in /home
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

As a general best practice, users should do production runs from
``$SCRATCH`` instead of ``$HOME``.

``$HOME`` is meant for permanent and relatively small storage. It is
  not
tuned to perform well for parallel jobs. Home is perfect for storing
files such as source codes and shell scripts, etc. Please note that
while building software in /global/home is generally good, it is best
to install dynamic libraries that are used on compute nodes
in `global common <../../filesystems/global-common>`__ for best
performance.

``$SCRATCH`` is meant for large and temporary storage. It is optimized
for read and write operations. ``$SCRATCH`` is perfect for staging
  data
and performing parallel computations. Running in ``$SCRATCH`` also
  helps
to improve the responsiveness of the global file systems (global homes
and global project) in general.

Time Limits
~~~~~~~~~~~

Due to backfill scheduling, short and variable-length jobs generally
start quickly resulting in much better job throughput.

.. code:: slurm

    #SBATCH --time-min=<lower_bound>
    #SBATCH --time=<upper_bound>

Long Running Jobs
~~~~~~~~~~~~~~~~~

Simulations which must run for a long period of time achieve the best
throughput when composed of many small jobs utilizing
checkpoint/restart chained together.

-  `Example: job chaining <examples/index.md#dependencies>`__

Improve efficiency by preparing user environment before running
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

When compute nodes are allocated for a batch job, all commands other
  than the
``srun`` command, such as: loading modules, setting up runtime
  environment
variables, compiling applications, and preparing input data, etc.,
  will run on
the head compute node (the first compute node in the pool of allocated
  nodes).
Running on a compute node is much more inefficient than running
on a login node. It also creates a burden on the global home file
  system.

Using the `Linux here
  document <https://en.wikipedia.org/wiki/Here_document>`__
as in the example below will run those commands to prepare the user
environment for the batch job on the login node to help improve job
  efficiency
and save computing cost of the batch job. It can also help to
  alleviate the
burden on the global home file system. This script also keeps the user
environment needed for the batch job in a single file.

!!! Example
This is an example to prepare the user environment on a login node,
propagate this environment to a batch job, and submit the batch job.
  This
can be accomplished in a single script.

::

    You could do so by preparing a file named "prepare-env.sh" in the example
    below, and running it as "./prepare-env.sh" on a login node. This script:

    * Sets up the user environment for the batch job first on a login node,
      such as loading modules, setting environment variables, or copying input
      files, etc.;
    * Creates a batch script named "prepare-env.sl";
    * Submits "prepare-env.sl", this job will inherit the user environment
      just set earlier in the script. 

::

    --8<-- "docs/jobs/examples/prepare-env/prepare-env.sh"

I/O performance
~~~~~~~~~~~~~~~

Cori has dedicated large, local, parallel scratch file systems. The
scratch file systems are intended for temporary uses such as storage
of checkpoints or application input and output. Data and I/O intensive
applications should use the local scratch (or Burst Buffer)
filesystems.

These systems should be referenced with the environment variable
``$SCRATCH``.

!!! tip
On Cori
the `Burst Buffer <examples/index.md#burst-buffer-test>`__ offers the
best I/O performance.

!!! warn
Scratch filesystems are not backed up and old files are
subject to purging.

Large Jobs
~~~~~~~~~~

Large jobs may take longer to start up, especially on KNL nodes. The
srun option ``--bcast=<destination_path>`` is recommended for large
  jobs
requesting over 1500 MPI tasks. By default, Slurm loads the executable
to the allocated compute nodes from the current working directory;
this may take long time when the file system (where the executable
resides) is slow. With the ``--bcast=/tmp/myjob``, the executable will
be copied to the ``/tmp/myjob`` directory. Since ``/tmp`` is part of
  the
memory on the compute nodes, it can speed up the job startup time.

.. code:: bash

    sbcast --compress=lz4 /path/to/exe /tmp/exe
    srun /tmp/exe

Network Locality
~~~~~~~~~~~~~~~~

For jobs which are sensitive to interconnect (MPI) performance and
utilize less than ~300 nodes it is possible to request that all nodes
are in a single Slingshot dragonfly group.

Slurm has a concept of "switches" which on Cori are configured to map
to Aries electrical groups. Since this places an additional constraint
on the scheduler a maximum time to wait for the requested topology can
be specified.

!!! example
Wait up to 60 minutes
``slurm     sbatch --switches=1@60 job.sh``

!!! info "Additional details and information"
\* `Cray XC Series Network
  (pdf) <https://www.cray.com/sites/default/files/resources/CrayXCNetwork.pdf>`__

Core specialization
~~~~~~~~~~~~~~~~~~~

Core specialization is a feature designed to isolate system overhead
(system interrupts, etc.) to designated cores on a compute node. It is
generally helpful for running on KNL, especially if the application
does not plan to use all physical cores on a 68-core compute node.
  Setting
aside 2 or 4 cores for core specialization is recommended.

The ``srun`` flag for core specialization is ``-S`` or
  ``--core-spec``. It
only works in a batch script with ``sbatch``. It can not be requested
  as
a flag with ``salloc`` for interactive jobs, since ``salloc`` is
  already a
wrapper script for ``srun``.

-  `Example <examples/index.md#core-specialization>`__

Process Placement
~~~~~~~~~~~~~~~~~

Several mechanisms exist to control process placement on ARCHER2's Cray
systems. Application performance can depend strongly on placement
depending on the communication pattern and other computational
characteristics.

Examples are run on Cori.

Default
^^^^^^^

::

    user@nid01041:~> srun -n 8 -c 2 check-mpi.intel.cori|sort -nk 4
    Hello from rank 0, on nid01041. (core affinity = 0-63)
    Hello from rank 1, on nid01041. (core affinity = 0-63)
    Hello from rank 2, on nid01111. (core affinity = 0-63)
    Hello from rank 3, on nid01111. (core affinity = 0-63)
    Hello from rank 4, on nid01118. (core affinity = 0-63)
    Hello from rank 5, on nid01118. (core affinity = 0-63)
    Hello from rank 6, on nid01282. (core affinity = 0-63)
    Hello from rank 7, on nid01282. (core affinity = 0-63)

``MPICH_RANK_REORDER_METHOD``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``MPICH_RANK_REORDER_METHOD`` environment variable is used to
specify other types of MPI task placement. For example, setting it to
0 results in a round-robin placement:

::

    user@nid01041:~> MPICH_RANK_REORDER_METHOD=0 srun -n 8 -c 2 check-mpi.intel.cori|sort -nk 4
    Hello from rank 0, on nid01041. (core affinity = 0-63)
    Hello from rank 1, on nid01111. (core affinity = 0-63)
    Hello from rank 2, on nid01118. (core affinity = 0-63)
    Hello from rank 3, on nid01282. (core affinity = 0-63)
    Hello from rank 4, on nid01041. (core affinity = 0-63)
    Hello from rank 5, on nid01111. (core affinity = 0-63)
    Hello from rank 6, on nid01118. (core affinity = 0-63)
    Hello from rank 7, on nid01282. (core affinity = 0-63)

There are other modes available with the ``MPICH_RANK_REORDER_METHOD``
environment variable, including one which lets the user provide a file
called ``MPICH_RANK_ORDER`` which contains a list of each task's
placement on each node. These options are described in detail in the
``intro_mpi`` man page.

**``grid_order``**

For MPI applications which perform a large amount of nearest-neighbor
communication, e.g., stencil-based applications on structured grids,
Cray provides a tool in the ``perftools-base`` module called
``grid_order`` which can generate a ``MPICH_RANK_ORDER`` file
  automatically
by taking as parameters the dimensions of the grid, core count,
etc. For example, to place MPI tasks in row-major order on a Cartesian
grid of size $(4, 4, 4)$, using 32 tasks per node on Cori:

::

    cori$ module load perftools-base
    cori$ grid_order -R -c 32 -g 4,4,4
    # grid_order -R -Z -c 32 -g 4,4,4
    # Region 3: 0,0,1 (0..63)
    0,1,2,3,16,17,18,19,32,33,34,35,48,49,50,51,4,5,6,7,20,21,22,23,36,37,38,39,52,53,54,55
    8,9,10,11,24,25,26,27,40,41,42,43,56,57,58,59,12,13,14,15,28,29,30,31,44,45,46,47,60,61,62,63

One can then save this output to a file called ``MPICH_RANK_ORDER``
  and
then set ``MPICH_RANK_REORDER_METHOD=3`` before running the job, which
tells Cray MPI to read the ``MPICH_RANK_ORDER`` file to set the MPI
  task
placement. For more information, please see the man page
  ``man grid_order`` (available when the ``perftools-base`` module is
  loaded) on
Cori.

Hugepages
~~~~~~~~~

Huge pages are virtual memory pages which are bigger than the default
page size of 4K bytes. Huge pages can improve memory performance
for common access patterns on large data sets since it helps to reduce
the number of virtual to physical address translations than compated
  with
using the default 4K. Huge pages also
increase the maximum size of data and text in a program accessible by
the high speed network, and reduce the cost of accessing memory, such
  as
in the case of many MPI\_Alltoall operations. Using hugepages
can help to `reduce the application runtime
  variability <../performance/variability.md>`__.

To use hugepages for an application (with the 2M hugepages as an
example):

::

    module load craype-hugepages2M
    cc -o mycode.exe mycode.c

And also load the same hugepages module at runtime.

The craype-hugepages2M module is loaded by deafult on Cori.
Users could unload the craype-hugepages2M module explicitly to disable
  the hugepages usage.

!!! note
The craype-hugepages2M module is loaded by default since the Cori CLE7
  upgrade on July 30, 2019.

Due to the hugepages memory fragmentation issue, applications may get
"Cannot allocate memory" warnings or errors when there are not enough
hugepages on the compute node, such as:

::

    libhugetlbfs [nid000xx:xxxxx]: WARNING: New heap segment map at 0x10000000 failed: Cannot allocate memory

The verbosity level of libhugetlbfs HUGETLB\_VERBOSE is set to 0 on Cori
by default to surpress debugging messages. Users can adjust this value
to obtain more info.

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
   variable HUGETLB\_RESTRICT\_EXE can be used to specify the susbset of
   the programs to use hugepages.
-  For certain applications if using hugepages either causes issues or
   slowing
   down performances, users can explicitly unload the craype-hugepages2M
   module.
   One such example is that when an application forks more subprocesses
   (such as
   pthreads) and allocate memory, the newly allocated memory are the
   small 4K
   pages.

Task Packing
~~~~~~~~~~~~

Users requiring large numbers of single-task jobs have several options
  at
ARCHER2. The options include:

-  Submitting jobs to the `shared QOS <examples/index.md#shared>`__,
-  Using a `workflow tool <workflow-tools.md>`__ to combine the tasks
   into one
   larger job,
-  Using `job arrays <examples/index.md#job-arrays>`__ to submit many
   individual
   jobs which look very similar.

If you have a large number of indpendent serial jobs (that is, the
  jobs do not
have dependencies on each other), you may wish to pack the individual
  tasks
into one bundled Slurm job to help with queue throughput. Packing
  multiple
tasks into one Slurm job can be done via multiple ``srun`` commands in
  the same
job script
(`example <examples/index.md#multiple-parallel-jobs-simultaneously>`__).

Basic SLURM commands
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

  [user@archer2 ~]$ sinfo 

``sbatch``: submitting jobs
~~~~~~~~~~~~~~~~~~~~~~~~~~~

``sbatch`` is used to submit a job script to the job submission system. The script
will typically contain one or more ``srun`` commands to launch parallel tasks.

When you submit the job, the scheduler provides the job ID, which is used to identify
this job in other Slurm commands and when looking at resource usage in SAFE.

::

  [user@archer2 ~]$ sbatch test-job.slurm
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

.. TODO: Add in partition and QOS limits once they are known

Troubleshooting
---------------

Slurm error messages
~~~~~~~~~~~~~~~~~~~~

.. TODO: add in examples of common Slurm error messages

Slurm queued reasons
~~~~~~~~~~~~~~~~~~~~

.. TODO explain ``Reason`` column from ``squeue``

Output from Slurm jobs
----------------------

Slurm places standard output (STDOUT) and standard error (STDERR) for each
job in the file ``slurm_<Job ID>.out``. This file appears in the
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

  - ``--account=<budget ID>`` your budget ID is usually something like
    ``t01`` or ``t01-test``. You can see which budget codes you can 
    charge to in SAFE.

Other common options that are used are:

  - ``--time=<hh:mm:ss>`` the maximum walltime for your job. *e.g.* For a 6.5 hour
    walltime, you would use ``--time=6:30:0``.
  - ``--name=<jobname>`` set a name for the job to help identify it in 
    Slurm command output.

In addition, parallel jobs will also need to specify how many nodes,
parallel processes and threads they require.

  - ``--nodes=<nodes>`` the number of nodes to use for the job.
  - ``--tasks-per-node=<processes per node>`` the number of parallel processes
    (e.g. MPI ranks) per node.
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
compute nodes. As well as launching the executable, ``srun`` also allows you
to specify the distribution and placement (or *pinning*) of the parallel
processes and threads.

This section describes how to use the ``srun`` command within your job
submission scripts on ARCHER2.

.. TODO: Description of ``srun`` options

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

    # Slurm job options (name, compute nodes, job time)
    #SBATCH --name=Example_MPI_Job
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
    srun ./my_mpi_executable.x

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
a factor of 18.

In the example below, we are using 4 nodes for 6 hours. There are 4 MPI
processes in total and 18 OpenMP threads per MPI process. Note the use
of the ``omplace`` command to specify the number of threads.

::

    #!/bin/bash --login

    # PBS job options (name, compute nodes, job time)
    #PBS -N Example_MixedMode_Job
    # Select 4 full nodes
    #PBS -l select=4:ncpus=36
    # Parallel jobs should always specify exclusive node access
    #PBS -l place=scatter:excl
    #PBS -l walltime=6:0:0

    # Replace [budget code] below with your project code (e.g. t01)
    #PBS -A [budget code]

    # Change to the directory that the job was submitted from
    cd $PBS_O_WORKDIR

    # Load any required modules
    module load mpt
    module load intel-compilers-17

    # Set the number of threads to 18
    #   There are 18 OpenMP threads per MPI process
    export OMP_NUM_THREADS=18

    # Launch the parallel job
    #   Using 8 MPI processes
    #   2 MPI processes per node
    #   18 OpenMP threads per MPI process
    #
    #   '-ppn' option is required for all HPE MPT jobs otherwise you will get an error similar to:
    #       'mpiexec_mpt error: Need 36 processes but have only 1 left in PBS_NODEFILE.'
    #
    mpiexec_mpt -ppn 2 -n 8 omplace -nt 18 ./my_mixed_executable.x arg1 arg2 > my_stdout.txt 2> my_stderr.txt

.. warning:: You must use the ``-ppn`` option when using HPE MPT otherwise you will see an error similar to: *mpiexec_mpt error: Need 36 processes but have only 1 left in PBS_NODEFILE.*

Job arrays
----------

The PBSPro job scheduling system offers the *job array* concept,
for running collections of almost-identical jobs, for example
running the same program several times with different arguments
or input data.

Each job in a job array is called a *subjob*.  The subjobs of a job
array can be submitted and queried as a unit, making it easier and
cleaner to handle the full set, compared to individual jobs.

All subjobs in a job array are started by running the same job script.
The job script also contains information on the number of jobs to be
started, and PBSPro provides a subjob index which can be passed to
the individual subjobs or used to select the input data per subjob.


Job script for a job array
~~~~~~~~~~~~~~~~~~~~~~~~~~

As an example, to start 56 subjobs, with the subjob index as the only
argument, and 4 hours maximum runtime per subjob, save the following
content into the file job_script.pbs:

::

    #!/bin/bash --login
    #PBS -l select=1:ncpus=1
    #PBS -l walltime=04:00:00
    #PBS -J 1-56
    #PBS -q workq
    #PBS -V

    cd ${PBS_O_WORKDIR}

    /path/to/exe $PBS_ARRAY_INDEX

Another example of a job script for submitting a job array is given
`here <../software-packages/flacs.html#submitting-many-flacs-jobs-as-a-job-array>`_.


Starting a job array
~~~~~~~~~~~~~~~~~~~~

When starting a job array, most options can be included in the job
file, but the project code for the resource billing has to be
specified on the command line:

::

    qsub -A [project code] job_script.pbs


Querying a job array
~~~~~~~~~~~~~~~~~~~~

In the normal PBSPro job status, a job array will be shown as a single
line:

::

    > qstat       
    Job id            Name           User   Time Use S Queue
    ----------------  -------------- ------ -------- - -----
    112452[].indy2-lo dispsim        user1         0 B workq

To monitor the subjobs of the job 112452, use

::

    > qstat -t 1235[]
    Job id            Name             User              Time Use S Queue
    ----------------  ---------------- ----------------  -------- - -----
    112452[].indy2-lo dispsim          user1                    0 B flacs           
    112452[1].indy2-l dispsim          user1             02:45:37 R flacs           
    112452[2].indy2-l dispsim          user1             02:45:56 R flacs           
    112452[3].indy2-l dispsim          user1             02:45:33 R flacs           
    112452[4].indy2-l dispsim          user1             02:45:45 R flacs           
    112452[5].indy2-l dispsim          user1             02:45:26 R flacs           
    ...


Interactive Jobs
----------------

When you are developing or debugging code you often want to run many
short jobs with a small amount of editing the code between runs. This
can be achieved by using the login nodes to run MPI but you may want
to test on the compute nodes (e.g. you may want to test running on 
multiple nodes across the high performance interconnect). One of the
best ways to achieve this on Cirrus is to use interactive jobs.

An interactive job allows you to issue ``mpirun_mpt`` commands directly
from the command line without using a job submission script, and to
see the output from your program directly in the terminal.

To submit a request for an interactive job reserving 8 nodes
(288 physical cores) for 1 hour you would
issue the following qsub command from the command line:

::

    qsub -IVl select=8:ncpus=36,walltime=1:0:0,place=scatter:excl -A [project code]

When you submit this job your terminal will display something like:

::

    qsub: waiting for job 19366.indy2-login0 to start

It may take some time for your interactive job to start. Once it
runs you will enter a standard interactive terminal session.
Whilst the interactive session lasts you will be able to run parallel
jobs on the compute nodes by issuing the ``mpirun_mpt``  command
directly at your command prompt (remember you will need to load the
``mpt`` module and any compiler modules before running)  using the
same syntax as you would inside a job script. The maximum number
of cores you can use is limited by the value of select you specify
when you submit a request for the interactive job.

If you know you will be doing a lot of intensive debugging you may
find it useful to request an interactive session lasting the expected
length of your working session, say a full day.

Your session will end when you hit the requested walltime. If you
wish to finish before this you should use the ``exit`` command.

Reservations
------------

Resource reservations are available on Cirrus. These allow users to reserve
a number of nodes for a specified length of time starting at a particular
time on the system.

Examples of the reasons for using reservations could be:

* An exceptional job requires longer than 96 hours runtime.
* You require a job/jobs to run at a particular time e.g. for a demonstration or course.

.. warning::

   For multi-node jobs we strongly recommend requesting a reservation two nodes larger
   than the size you want to stop the reservation failing if a node crashes. This is
   particularly important if the reservation involves long jobs or those of a time
   critical nature.

.. note::

   Reservations will be charged at 1.5 times the usual rate and you
   will be charged the full rate for the entire reservation whether or not you use the
   resources reserved for the full time. In addition, you will not be refunded the resources
   if you fail to use them due to a job crash unless this crash is due to a system failure.
   To allow people to create multi-node reservations, we will charge at number of nodes - 2
   for resevations (with a minimum of 2 nodes charged).

Requesting reservations
~~~~~~~~~~~~~~~~~~~~~~~

You request a reservation on Cirrus using PBS from the command line. Before 
requesting the reservation, you will need the following information:

* The start time for the resevation
* The duration of the reservation 
* The number of cores (or nodes for multi-node, node-exclusive jobs)
* The project ID you wish to charge the reservation to

You use the ``pbs_rsub`` command to create a reservation. This command has a similar
syntax to the ``qsub`` command for requesting resources but takes the additional
parameters ``-R`` (to specify the reservaiton start time); ``-D`` (to specify the reservation
duration); and ``-G`` (to specify the project ID to charge the reservation to). For example,
to create a reservation for 3 hours at 10:30 (UK time) on Saturday 26 August 2017 for 4
full nodes (144 physical cores, 288 hyperthreads) and charge to project "t01" you would use the command:

::

   pbs_rsub -R 1708261030 -D 3:0:0 -l select=6:ncpus=36,place=scatter:excl -G +t01
   
Generating response:

::

   R122604.indy2-login0 UNCONFIRMED

The command will return a reservation ID (``R122604`` in the example above) and note that 
it is currently ``UNCONFIRMED``. PBSPro will change the status to ``CONFIRMED`` once it 
has checked that it is possible to schedule the reservation. Note that we requested 6 nodes
rather than the required 4 to reduce the risk of hardware failure affecting the reservation.

.. note::

   Only the user that requested this reservation will be able to submit jobs to it. To
   create a reservation that is available to all users in a particular project, see the instructions
   below.

There are many other options to the ``pbs_rsub`` command. Please check the man page for
a full description.

Checking the status of your reservation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can cheack the status of your reservation request with the ``pbs_rstat`` command:

::

   pbs_rstat
   
Which will generate a response:

::

   Resv ID    Queue    User     State             Start / Duration / End              
   ---------------------------------------------------------------------
   R122604.in R122605  auser@ CO            Sat 10:30 / 10800 / Sat 13:30 

and, as you can see, the status of the requested reservation is now ``CO`` (``CONFIRMED``).

Submitting jobs to a reservation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You submit jobs to reservations in the same way as you do for all other jobs using the
``qsub`` command. The only additional information required is to specify the reservation
ID to the ``-q`` option. For example, to submit to the reservation created above you would
use:

::

   qsub -q R122604 ...usual qsub options/job script name...


.. note::

   You can submit jobs to the reservation ahead of the start time and the job will 
   start as soon as the reservation begins.

Reservations for all project users
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

By default, a reservation will only be available to the user who requested it. If you wish
to create a reservation that is usable by all members of your project you need to modify
the user permissions using the ``-U`` option.

For example, to create a reservation for 192 hours, starting at 16:15 (UK time) on Monday 18
September 2017 for 64 nodes accessible by all users in the t01 project you would use:

::

   pbs_rsub -R 1709181615 -D 192:0:0 -l select=66:ncpus=36,place=scatter:excl -G +t01 -U +
   
Generating a response:

::

   R122605.indy2-login0 UNCONFIRMED

Here, the ``-G +t01`` option charges the reservation to the t01 project **and** restricts access to
users in the ``t01`` project; the ``-U +`` option allows all users (in the t01 project) access 
to the reservation. Note that, as above, we created the reservation with 66 nodes instead of the
required 64 to reduce the risk of hardware failures affecting the reservation.

.. note::

   You can restrict access to specific users within a project, see the pbs_rsub man 
   page for more information on how to do this.

Deleting a reservation
~~~~~~~~~~~~~~~~~~~~~~

Use the ``pbs_rdel`` command to delete a reservation:

::

   [auser@cirrus-login0 ~]$ pbs_rdel R122605

