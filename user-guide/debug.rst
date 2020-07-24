Debugging
=========

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

The following debugging tools are available on ARCHER2:

* `gdb4hpc`_ is a command-line debugging tool provided by Cray. It works similarly to
  `gdb<https://www.gnu.org/software/gdb/>`_, but allows the user to debug multiple parallel processes
  without multiple windows. gdb4hpc can be used to investigate deadlocked code, segfaults, and other
  errors for C/C++ and Fortran code. Users can single-step code and focus on specific processes groups
  to help identify unexpected code behavior. (text from `ALCF<https://www.alcf.anl.gov/support-center/theta/gdb>`_).
* **valgrind4hpc** is a parallel memory debugging tool that aids in detection of memory leaks and
  errors in parallel applications. It aggregates like errors across processes and threads to simply
  debugging of parallel appliciations.
* `STAT`_ generate merged stack traces for parallel applications. Also has visualisation tools.
* **ATP** scalable core file and backtrace analysis when parallel programs crash.
* **CCDB** Cray Comparative Debugger. Compare two versions of code side-by-side to analyse differences.

gdb4hpc
-------

The GNU Debugger for HPC (gdb4hpc) is a GDB-based debugger used to debug applications compiled with CCE, PGI, GNU, and Intel Fortran, C and C++ compilers. It allows programmers to either launch an application within it or to attach to an already-running application. Attaching to an already-running and hanging application is a quick way of understanding why the application is hanging, whereas launching an application through gdb4hpc will allow you to see your application running step-by-step, output the values of variables, and check whether the application runs as expected.

Setting up for gdb4hpc
~~~~~~~~~~~~~~~~~~~~~~

For your executable to be compatible with gdb4hpc, you will need to compile your code with the debugging flag ``-g``:

::

    cc -g mu_program.c -o my_executable
    
To use the gdb4hpc tool, you need to request an allocation interactive session:

::

    salloc --nodes=2 --tasks-per-node=128 --cpus-per-task=1 --time=01:00:00 --account=[budget code]
    
You will know your session has launched when getting the message:

::

    salloc: Granted job allocation 925
    
Once you've launched your interactive session and navigated to the ``/work`` directory where you will run your code. You will need to load the ``gdb4hpc`` module:

::

    module load gdb4hpc
    
Attaching with gdb4hpc
~~~~~~~~~~~~~~~~~~~~~~

Attaching to a hanging job using gdb4hpc is a great way of seeing which state each processor is in. However, this does not produce the most visually appealing results. For a more easy-to-read program, please take a look at `STAT`_

In your interactive session, launch your executable as a background task (by adding an ``&``  at the end of the command). For example, if you are running an executable called ``my_exe`` using 256 processes, you would run:

::

    srun -n 256 ./my_exe &
    
You will need to get the full job ID of the job you have just launched. To do this, run:

::

    squeue -u $USER
    
and find the job ID associated with this interactive session -- this will be the one with the jobname ``bash``. In this example:

::

    JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
    1050     workq my_mpi_j   jsindt  R       0:16      1 nid000001
    1051     workq     bash   jsindt  R       0:12      1 nid000002
    
the appropriate job id is 1051. Next, you will need to run ``sstat`` on this job id:

::

    sstat 1051
    
This will output a large amount of information about this specific job. We are looking for the first number of this ouput, which should look like ``JOB_ID.##``  -- the number after the job ID is the number of slurm tasks performed in this interactive session. For our example (where ``srun`` is the first slurm task performed), the number is 1051.0.

Launch ``gdb4hpc``:

::
    
    gdb4hpc
    
You will get some information about this version of the program and, eventually, you will get a command prompt:

::

  gdb4hpc 4.5 - Cray Line Mode Parallel Debugger
  With Cray Comparative Debugging Technology.
  Copyright 2007-2019 Cray Inc. All Rights Reserved.
  Copyright 1996-2016 University of Queensland. All Rights Reserved.
  Type "help" for a list of commands.
  Type "help <cmd>" for detailed help about a command.
  dbg all>
  
We will be using the ``attach`` command to attach to our program that hangs. This is done by writing:

::
   dbg all> attach $my_prog JOB_ID.##
   
where JOB_ID.## is the full job ID found using ``sstat`` (in our example, this would be 1051.0). The name ``$my_prog`` is a dummy-name -- it could be whatever name you like.

As it is attaching, gdb4hpc will output text to screen that looks like:

::

    Attaching to application, please wait...
    Creating MRNet communication network...
    Waiting for debug servers to attach to MRNet communications network...
    Timeout in 400 seconds. Please wait for the attach to complete.
    Number of dbgsrvs connected: [0];  Timeout Counter: [1]
    
    ...
    
    Finalizing setup...
    Attach complete.
    Current rank location:

After this, you will get an output that, amongst other things, tells you which line of your code each process is on, and what each process is doing. This can be helpful to see where the hang-up is.

If you accidentally attached to the wrong job, you can detach by running:

::

    dbg all> release $my_prog
    
and re-attach with the correct job ID. You will need to change your dummy name from ``$my_prog`` to something else.

When you are finished using ``gbd4hpc``, simply run:

::

  dbg all> quit
  
Do not forget to exit your interactive session.

Launching through gdb4hpc
~~~~~~~~~~~~~~~~~~~~~~~~~

In your interactive session, launch gdb4hpc:

::

    gdb4hpc
    
STAT
----

The Stack Trace Analysis Tool (STAT) is a cross-platform debugging tool from the University of Wisconsin-Madison. ATP is based on the same technology as STAT, both are designed to gather and merge stack traces from a running application's parallel processes. The STAT tool can be useful when application seems to be deadlocked or stuck, i.e. they don't crash but they don't progress as expected, and it has been designed to scale to a very large number of processes. Full information on STAT, including use cases, is available at the `STAT website <https://hpc.llnl.gov/software/development-environment-software/stat-stack-trace-analysis-tool>`_.

STAT will attach to a running program and query that program to find out where all the processes in that program currently are. It will then process that data and produce a graph displaying the unique process locations (i.e. where all the processes in the running program currently are). To make this easily understandable it collates together all processes that are in the same place providing only unique program locations for display. 

Using STAT on ARCHER2
~~~~~~~~~~~~~~~~~~~~~
To use the stat tool, you need to request an allocation session:

::

    salloc --nodes=2 --tasks-per-node=128 --cpus-per-task=1 --time=01:00:00 --account=[budget code]
    
You will know your session has launched when getting the message:

::

    salloc: Granted job allocation 925
    
Once you've launched your interactive session and navigated to the ``/work`` directory where you will run your code. You will need to load the ``cray-stat`` module:

::

    module load cray-stat
    
Then, launch your job as normal, but as a background task (by adding an ``&`` at the end of the command). For example, if you are running an executable called ``my_exe`` using 256 processes, you would run:

::

    srun -n 256 ./my_exe &
    
You will need the Program ID (PID) of the job you have just launched -- the PID is printed to sreen upon launch, or you can get it by running:

::

    ps -u $USER
    
This will present you with a set of text that looks like this:

::

       PID TTY          TIME CMD
    154296 ?        00:00:00 systemd
    154297 ?        00:00:00 (sd-pam)
    154302 ?        00:00:00 sshd
    154303 pts/8    00:00:00 bash
    157150 pts/8    00:00:00 salloc
    157152 pts/8    00:00:00 bash
    157183 pts/8    00:00:00 srun
    157185 pts/8    00:00:00 srun
    157191 pts/8    00:00:00 ps

Once your application has reached the point where it hangs, issue the following comman (replacing PID with the ID of the **first** srun task -- in the above example, I would replace PID with 157183):

::

    stat-cl -i PID
    
You will get an output that looks like this:

::

    STAT started at 2020-07-22-13:31:35
    Attaching to job launcher (null):157565 and launching tool daemons...
    Tool daemons launched and connected!
    Attaching to application...
    Attached!
    Application already paused... ignoring request to pause
    Sampling traces...
    Traces sampled!
    Resuming the application...
    Resumed!
    Pausing the application...
    Paused!
    
    ...
    
    Detaching from application...
    Detached!
    
    Results written to $PATH_TO_RUN_DIRECTORY/stat_results/my_exe.0000

Once STAT is finished, you can kill the srun job and exit the salloc job using the following commands (again replacing PID with the first srun ID):

::

    kill -9 PID
    exit
    
From the login node, you can view the results that STAT has produced using the following command (note that "my_exe" will need to be replaced with the name of the executable you ran):

::

    stat-view stat_results/my_exe.0000/00_my_exe.0000.3D.dot
    
This produces a graph displaying all the different places within the program that the parallel processes were when you queried them.

..note::

  To see the graph, you will need to have exported your X display.
