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

Standard compute node queues
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

There are a number of queues available to general users on Cirrus that route jobs to the standard
compute nodes. Standard jobs
are automatically routed into either ``workq``, ``indy`` or ``large``  depending on the project 
that submitted the job and the size of the job. To use any of these queues, you **should not** specify 
a queue name in your job script.

* ``workq``: Jobs in this queue can have a maximum walltime of 96 hours (4 days) and a maximum job size of 2520 cores (70 
  nodes). **Each project** can use a maximum of 2520 cores (70 nodes) summed across all their running jobs at any one time
  and **each user** can have a maximum of 20 jobs running (note that these limits can be dynamically altered by the service to improve throughput on the system). Jobs running in this queue are node shared by default (i.e.
  multiple jobs can share a single compute node). If you want to use node exclusive then you must specify this using the PBS
  options described below.
* ``indy``: Jobs in this queue have a variable maximum job size and walltime (use `qstat -Qf indy` on Cirrus to check the current limits. Jobs running in this queue are node shared by default (i.e.
  multiple jobs can share a single compute node). If you want to use node exclusive then you must specify this using the PBS
  options described below. This queue is only available to projects from industrial clients.
* ``large``: There is no 
  upper limit on job size in this queue but there is a minimum job size of 2521
  cores (71 nodes), a maximum walltime of 48 hours (2 days),
  **each user** can have a maximum of 1 job running at any one time, and a maximum
  of 4 jobs in the queue (including a running job). Jobs running in this queue are node shared by default (i.e.
  multiple jobs can share a single compute node). If you want to use node exclusive then you must specify this using the PBS
  options described below.

GPU compute node queues
~~~~~~~~~~~~~~~~~~~~~~~

If you wish to use the GPU compute nodes then you need to submit to the ``gpu`` queue by adding 
``-q gpu`` to your submission. You will also need to specify how many GPU accelerators you wish to
use. Full details are available in the GPU chapter of this User Guide. 

Jobs in the ``gpu`` queue can havea maximum walltime of 6 hours. There is a maximum job size of 2
nodes (as there are 2 nodes available). Each user can only have one job running at any one time.

Queue error messages
~~~~~~~~~~~~~~~~~~~~

If you try to submit a job that asks for more than the maximum allowed wall
time or cores you will see an error similar to:

::

    [user@cirrus-login0 ~]$ qsub submit.pbs 
    qsub: Job violates queue and/or server resource limits

Output from PBS jobs
--------------------

PBS produces standard output and standard error for each batch job can
be found in files ``<jobname>.o<Job ID>`` and ``<jobname>.e<Job ID>``
respectively. These files appear in the job's working directory once
your job has completed or its maximum allocated time to run (i.e. wall
time, see later sections) has ran out.

Running Parallel Jobs
---------------------

This section describes how to write job submission scripts specifically
for different kinds of parallel jobs on Cirrus.

All parallel job submission scripts require (as a minimum) you to
specify four things:

-  The number of nodes and cores per node you require via the
   ``-l select=[Nodes]:ncpus=36`` option. Each node has 36 physical
   cores (2x 18-core sockets). For example, to select 4 nodes
   (144 physical cores in total) you would use
   ``-l select=4:ncpus=36``. **We strongly recommend that all parallel
   jobs use node exclusive mode as described below to get best performance.**
-  The placement option ``-l place=scatter`` to ensure that parallel
   processes/threads are scheduled to the full set of compute nodes
   assigned to the job.
-  The maximum length of time (i.e. walltime) you want the job to run
   for via the ``-l walltime=[hh:mm:ss]`` option. To ensure the
   minimum wait time for your job, you should specify a walltime as
   short as possible for your job (i.e. if your job is going to run for
   3 hours, do not specify 12 hours). On average, the longer the
   walltime you specify, the longer you will queue for.
-  The project code that you want to charge the job to via the
   ``-A [project code]`` option

In addition to these mandatory specifications, there are many other
options you can provide to PBS. The following options may be useful:

- The name for your job is set using ``-N My_job``. In the examples below
  the name will be "My\_job", but you can replace "My\_job" with any
  name you want. The name will be used in various places. In particular
  it will be used in the queue listing and to generate the name of your
  output and/or error file(s). Note there is a limit on the size of the
  name.

Exclusive Node Access
~~~~~~~~~~~~~~~~~~~~~

Exclusive node access means each node is dedicated to one user only.

To make sure your jobs have exclusive node access you should add the
``excl`` sharing directive to the ``place`` option in your jobs:

::

    #PBS -l place=scatter:excl

All of our example parallel job submission scripts below specify this option as
this mode of use is strongly recommended for all parallel jobs on Cirrus.

Running MPI parallel jobs
-------------------------

When you are running parallel jobs requiring MPI you will use an MPI launch
command to start your executable in parallel. The name and options for
this MPI launch command depend on which MPI library you are using:
HPE MPT (Message Passing Toolkit), Intel MPI or OpenMPI. We give details below
of the commands used in each case and our example job submission scripts
have examples for both libraries.

.. note:: If you are using a centrally-installed MPI software package you will need to know which MPI library was used to compile it so you can use the correct MPI launch command. You can find this information using the ``module show`` command. For example:

::

   [auser@cirrus-login0 ~]$ module show vasp
   -------------------------------------------------------------------
   /lustre/sw/modulefiles/vasp/5.4.4-intel17-mpt214:

   conflict	 vasp 
   module		 load mpt 
   module		 load intel-compilers-17 
   module		 load intel-cmkl-17 
   module		 load gcc/6.2.0 
   prepend-path	 PATH /lustre/home/y07/vasp5/5.4.4-intel17-mpt214/bin 
   setenv		 VASP5 /lustre/home/y07/vasp5/5.4.4-intel17-mpt214 
   setenv		 VASP5_VDW_KERNEL /lustre/home/y07/vasp5/5.4.4-intel17-mpt214/vdw_kernal/vdw_kernel.bindat 
   -------------------------------------------------------------------

This shows that VASP was compiled with HPE MPT (from the ``module load mpt`` in 
the output from the command. If a package was compiled with Intel MPI there 
would be ``module load intel-mpi-17`` in the output instead.

HPE MPT (Message Passing Toolkit)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

HPE MPT is accessed at both compile and runtime by loading the ``mpt`` module:

::

   module load mpt

HPE MPT: parallel launcher ``mpiexec_mpt``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The HPE MPT parallel launcher on Cirrus is ``mpiexec_mpt``.

.. note:: This parallel job launcher is only available once you have loaded the ``mpt`` module.

A sample MPI launch line using ``mpiexec_mpt`` looks like:

::

    mpiexec_mpt -ppn 36 -n 72 ./my_mpi_executable.x arg1 arg2

This will start the parallel executable "my\_mpi\_executable.x" with
arguments "arg1" and "arg2". The job will be started using 72 MPI
processes, with 36 MPI processes are placed on each compute node 
(this would use all the physical cores on each node). This would
require 2 nodes to be requested in the PBS options. Note that the ordering of flags is important.

The most important ``mpiexec_mpt`` flags are:

 ``-n [total number of MPI processes]``
    Specifies the total number of distributed memory parallel processes
    (not including shared-memory threads). For jobs that use all
    physical cores this will usually be a multiple of 36. The default on
    Cirrus is 1.
 ``-ppn [parallel processes per node]``
    Specifies the number of distributed memory parallel processes per
    node. There is a choice of 1-36 for physical cores on Cirrus compute
    nodes (1-72 if you are using Hyper-Threading) If you are running with
    exclusive node usage, the most economic choice is always to run with
    "fully-packed" nodes on all physical cores if possible, i.e.
    ``-ppn 36`` . Running "unpacked" or "underpopulated" (i.e. not using
    all the physical cores on a node) is useful if you need large
    amounts of memory per parallel process or you are using more than
    one shared-memory thread per parallel process.

.. note:: ``mpiexec_mpt`` only works from within a PBS job submission script.

.. warning:: You must use the ``-ppn`` option to the ``mpiexec_mpt`` command otherwise you will see an error similar to: *mpiexec_mpt error: Need 36 processes but have only 1 left in PBS_NODEFILE.*

.. warning:: When using the ``mpiexec_mpt`` command, the ``-ppn`` option must come before the ``-n`` option otherwise you will see an error similar to: *MPT ERROR: Not enough slots from job scheduler for requested ranks*. (This applies to the the default version of MPT and versions from 2.18 upwards.)

.. note:: If you are using an older version of MPT (2.17 or earlier), the ``-n`` option must come before the ``-ppn`` option when using the ``mpiexec_mpt`` command. If you get the options the wrong way around you will see an error similar to: *MPT ERROR: Not enough slots from job scheduler for requested ranks*

Please use ``man mpiexec_mpt`` query further options. (This is only available
once you have loaded the ``mpt`` module.)

HPE MPT: interactive MPI using ``mpirun``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you want to run short interactive parallel applications (e.g. for 
debugging) then you can run HPE MPT compiled MPI applications on the login
nodes using the ``mpirun`` command.

For instance, to run a simple, short 4-way MPI job on the login node, issue the
following command (once you have loaded the appropriate modules):

:: 

    mpirun -n 4 ./hello_mpi.x

.. note:: you should not run long, compute- or memory-intensive jobs on the login nodes. Any such processes are liable to termination by the system with no warning.


HPE MPT: running hybrid MPI/OpenMP applications
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you are running hybrid MPI/OpenMP code using HPE MPT you will also often make
use of the ``omplace`` tool in your job launcher line. This tool 
takes the number of threads as the option ``-nt``:

 ``-nt [threads per parallel process]``
    Specifies the number of cores for each parallel process to use for
    shared-memory threading. (This is in addition to the
    ``OMP_NUM_THREADS`` environment variable if you are using OpenMP for
    your shared memory programming.) The default on Cirrus is 1.

Please use ``man mpiexec_mpt`` and ``man omplace`` to query further options.
(Again, these are only available once you have loaded the ``mpt`` module.)

Intel MPI
~~~~~~~~~

Intel MPI is accessed at runtime by loading the ``intel-mpi-17``.

::

   module load intel-mpi-17

Intel MPI: parallel job launcher ``mpirun``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Intel MPI parallel job launcher on Cirrus is ``mpirun``.

.note :: This parallel job launcher is only available once you have loaded the ``intel-mpi-17`` module.

A sample MPI launch line using ``mpirun`` looks like:

::

    mpirun -n 72 -ppn 36 ./my_mpi_executable.x arg1 arg2

This will start the parallel executable "my\_mpi\_executable.x" with
arguments "arg1" and "arg2". The job will be started using 72 MPI
processes, with 36 MPI processes are placed on each compute node 
(this would use all the physical cores on each node). This would
require 2 nodes to be requested in the PBS options.

The most important ``mpirun`` flags are:

 ``-n [total number of MPI processes]``
    Specifies the total number of distributed memory parallel processes
    (not including shared-memory threads). For jobs that use all
    physical cores this will usually be a multiple of 36. The default on
    Cirrus is 1.
 ``-ppn [parallel processes per node]``
    Specifies the number of distributed memory parallel processes per
    node. There is a choice of 1-36 for physical cores on Cirrus compute
    nodes (1-72 if you are using Hyper-Threading) If you are running with
    exclusive node usage, the most economic choice is always to run with
    "fully-packed" nodes on all physical cores if possible, i.e.
    ``-ppn 36`` . Running "unpacked" or "underpopulated" (i.e. not using
    all the physical cores on a node) is useful if you need large
    amounts of memory per parallel process or you are using more than
    one shared-memory thread per parallel process.

Documentation on using Intel MPI (including ``mpirun``) can be found 
online at:

* `Intel MPI Documentation <https://software.intel.com/en-us/articles/intel-mpi-library-documentation>`__

Intel MPI: running hybrid MPI/OpenMP applications
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you are running hybrid MPI/OpenMP code using Intel MPI you need to 
set the ``I_MPI_PIN_DOMAIN`` environment variable to ``omp`` so that
MPI tasks are pinned with enough space for OpenMP threads.

For example, in your job submission script you would use:

::

   export I_MPI_PIN_DOMAIN=omp

You can then also use the ``KMP_AFFINITY`` enviroment variable 
to control placement of OpenMP threads. For more information, see:

* `Intel OpenMP Thread Affinity Control <https://software.intel.com/en-us/articles/openmp-thread-affinity-control>`__

Intel MPI: MPI-IO setup
^^^^^^^^^^^^^^^^^^^^^^^

If you wish to use MPI-IO with Intel MPI you must set a couple of 
additional environment variables in your job submission script to
tell the MPI library to use the Lustre file system interface.
Specifically, you should add the lines:

::

   export I_MPI_EXTRA_FILESYSTEM=on
   export I_MPI_EXTRA_FILESYSTEM_LIST=lustre

after you have loaded the ``intel-mpi-17`` module.

If you fail to set these environment variables you may see errors such as:

::

   This requires fcntl(2) to be implemented. As of 8/25/2011 it is not. Generic MPICH
   Message: File locking failed in
   ADIOI_Set_lock(fd 0,cmd F_SETLKW/7,type F_WRLCK/1,whence 0) with return value
   FFFFFFFF and errno 26.
   - If the file system is NFS, you need to use NFS version 3, ensure that the lockd
    daemon is running on all the machines, and mount the directory with the 'noac'
    option (no attribute caching).
   - If the file system is LUSTRE, ensure that the directory is mounted with the 'flock'
    option.
   ADIOI_Set_lock:: Function not implemented
   ADIOI_Set_lock:offset 0, length 10
   application called MPI_Abort(MPI_COMM_WORLD, 1) - process 3

OpenMPI
~~~~~~~~~

OpenMPI is accessed at runtime by loading the module ``openmpi``. There are three OpenMPI modules currently installed::
  
 module load openmpi/2.1.0
 module load openmpi/3.1.4
 module load openmpi/4.0.1

``openmpi/2.1.0`` is installed to be primarily used with Singularity. For user applications not using Singularity the newer versions of OpenMPI should be selected, with ``openmpi/4.0.1`` being preferable.

OpenMPI: parallel job launcher ``mpirun``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The OpenMPI parallel job launcher on Cirrus is ``mpirun``.

.note :: This parallel job launcher is only available once you have loaded one of the OpenMPI modules.

A sample MPI launch line using ``mpirun`` looks like:

::

    mpirun -n 72 -N 36 ./my_mpi_executable.x arg1 arg2

This will start the parallel executable "my\_mpi\_executable.x" with
arguments "arg1" and "arg2". The job will be started using 72 MPI
processes, with 36 MPI processes are placed on each compute node 
(this would use all the physical cores on each node). This would
require 2 nodes to be requested in the PBS options.

The most important ``mpirun`` flags are:

 ``-n [total number of MPI processes]``
    Specifies the total number of distributed memory parallel processes
    (not including shared-memory threads). For jobs that use all
    physical cores this will usually be a multiple of 36.
 ``-N [parallel processes per node]``
    Specifies the number of distributed memory parallel processes per
    node. There is a choice of 1-36 for physical cores on Cirrus compute
    nodes (1-72 if you are using Hyper-Threading) If you are running with
    exclusive node usage, the most economic choice is always to run with
    "fully-packed" nodes on all physical cores if possible, i.e.
    ``-N 36`` . Running "unpacked" or "underpopulated" (i.e. not using
    all the physical cores on a node) is useful if you need large
    amounts of memory per parallel process or you are using more than
    one shared-memory thread per parallel process.

Note, to use OpenMPI the PBS batch script used for running parallel jobs must include the ``mpiprocs`` keyword when specifying the number of nodes and processes to run, i.e. to run on 2 nodes using 36 process on each node (72 in total), the PBS select line would be::

  #PBS -l select=2:ncpus=36:mpiprocs=36
    
Documentation on using OpenMPI (including ``mpirun``) can be found 
online at:

* `OpenMPI Documentation <https://www.open-mpi.org/doc/current/>`__



Example parallel MPI job submission scripts
-------------------------------------------

A subset of example job submssion scripts are included in full below. The
full set are available via the following links:


Example: HPE MPT job submission script for MPI parallel job
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A simple MPI job submission script to submit a job using 4 compute
nodes (maximum of 144 physical cores) for 20 minutes would look like:

::

    #!/bin/bash --login

    # PBS job options (name, compute nodes, job time)
    #PBS -N Example_MPI_Job
    # Select 4 full nodes
    #PBS -l select=4:ncpus=36
    # Parallel jobs should always specify exclusive node access
    #PBS -l place=scatter:excl
    #PBS -l walltime=00:20:00

    # Replace [budget code] below with your project code (e.g. t01)
    #PBS -A [budget code]             

    # Change to the directory that the job was submitted from
    cd $PBS_O_WORKDIR
  
    # Load any required modules
    module load mpt
    module load intel-compilers-17

    # Set the number of threads to 1
    #   This prevents any threaded system libraries from automatically 
    #   using threading.
    export OMP_NUM_THREADS=1

    # Launch the parallel job
    #   Using 144 MPI processes and 36 MPI processes per node
    #
    #   '-ppn' option is required for all HPE MPT jobs otherwise you will get an error similar to:
    #       'mpiexec_mpt error: Need 36 processes but have only 1 left in PBS_NODEFILE.'
    #
    mpiexec_mpt -ppn 36 -n 144 ./my_mpi_executable.x arg1 arg2 > my_stdout.txt 2> my_stderr.txt

This will run your executable "my\_mpi\_executable.x" in parallel on 144
MPI processes using 2 nodes (36 cores per node, i.e. not using hyper-threading). PBS will
allocate 4 nodes to your job and mpirun_mpt will place 36 MPI processes on each node
(one per physical core).

See above for a more detailed discussion of the different PBS options

.. warning:: You must use the ``-ppn`` option when using HPE MPT otherwise you will see an error similar to: *mpiexec_mpt error: Need 36 processes but have only 1 left in PBS_NODEFILE.*

Example: HPE MPT job submission script for MPI+OpenMP (mixed mode) parallel job
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

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


Example: OpenMPI job submission script for MPI parallel job
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A simple MPI job submission script to submit a job using 4 compute
nodes (maximum of 144 physical cores) for 20 minutes would look like:

::

    #!/bin/bash --login

    # PBS job options (name, compute nodes, job time)
    #PBS -N Example_MPI_Job
    # Select 4 full nodes
    #PBS -l select=4:ncpus=36:mpiprocs=36
    # Parallel jobs should always specify exclusive node access
    #PBS -l place=scatter:excl
    #PBS -l walltime=00:20:00

    # Replace [budget code] below with your project code (e.g. t01)
    #PBS -A [budget code]             

    # Change to the directory that the job was submitted from
    cd $PBS_O_WORKDIR
  
    # Load any required modules
    module load openmpi/4.0.1
    module load intel-compilers-17

    # Set the number of threads to 1
    #   This prevents any threaded system libraries from automatically 
    #   using threading.
    export OMP_NUM_THREADS=1

    # Launch the parallel job
    #   Using 144 MPI processes and 36 MPI processes per node
    #
    mpirun --mca pml ucx --mca btl ^openib -N 36 -n 144 ./my_mpi_executable.x arg1 arg2 > my_stdout.txt 2> my_stderr.txt

This will run your executable "my\_mpi\_executable.x" in parallel on 144
MPI processes using 2 nodes (36 cores per node, i.e. not using hyper-threading). PBS will
allocate 4 nodes to your job and mpirun will place 36 MPI processes on each node
(one per physical core).

Note the ``--mca pml ucx --mca btl ^openib`` part of the command above is only required for OpenMPI version 4.0.1. It is not required for the older versions of OpenMPI installed on ARCHER.
	     
Example: job submission script for parallel non-MPI based jobs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you want to run on multiple nodes, where each node is running a self-contained job, not using MPI
(e.g.) for processing data or a parameter sweep, you can use the HPE MPT ``mpiexec_mpt`` launcher to control job placement.

In the example script below, ``work.bash`` is a bash script which runs a threaded executable with a command-line input and
``perf.bash`` is a bash script which copies data from the CPU performance counters to an output file. As both handle the
threading themselves, it is sufficient to allocate 1 MPI rank. Using the ampersand ``&`` allows both to execute simultaneously.
Both ``work.bash`` and ``perf.bash`` run on 4 nodes.

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

   # Set this variable to inform mpiexec_mpt these are not MPI jobs
   export MPI_SHEPHERD=true

   # Execute work and perf scripts on nodes simultaneously.
   mpiexec_mpt -ppn 1 -n 4 work.bash &
   mpiexec_mpt -ppn 1 -n 4 perf.bash &
   wait

.note :: The ``wait`` command is required to stop the PBS job finishing before the scripts finish.  If you find odd behaviour, especially with respect to the values of bash variables, double check you have set ``MPI_SHEPHERD=true``

Serial Jobs
-----------

Serial jobs are setup in a similar way to parallel jobs on Cirrus. The
only changes are:

1. You should request a single core with ``select=1:ncpus=1``
2. You will not need to use a parallel job launcher to run your executable

A simple serial script to compress a file would be:

::

    #!/bin/bash --login

    # PBS job options (name, compute nodes, job time)
    #PBS -N Example_Serial_Job
    #PBS -l select=1:ncpus=1
    #PBS -l walltime=0:20:0

    # Replace [budget code] below with your project code (e.g. t01)
    #PBS -A [budget code]

    # Change to the directory that the job was submitted from
    cd $PBS_O_WORKDIR

    # Load any required modules
    module load intel-compilers-16

    # Set the number of threads to 1 to ensure serial
    export OMP_NUM_THREADS=1

    # Run the serial executable
    gzip my_big_file.dat

.. _jobarrays:

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

