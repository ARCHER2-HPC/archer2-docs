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
* `valgrind4hpc`_ is a parallel memory debugging tool that aids in detection of memory leaks and
  errors in parallel applications. It aggregates like errors across processes and threads to simply
  debugging of parallel applications.
* `STAT`_ generate merged stack traces for parallel applications. Also has visualisation tools.
* `ATP`_ scalable core file and backtrace analysis when parallel programs crash. Note that this is not currently working on ARCHER2.
.. * `CCDB`_ Cray Comparative Debugger. Compare two versions of code side-by-side to analyse differences.

gdb4hpc
-------

The GNU Debugger for HPC (gdb4hpc) is a GDB-based debugger used to debug applications compiled with CCE, PGI, GNU, and Intel Fortran, C and C++ compilers. It allows programmers to either launch an application within it or to attach to an already-running application. Attaching to an already-running and hanging application is a quick way of understanding why the application is hanging, whereas launching an application through gdb4hpc will allow you to see your application running step-by-step, output the values of variables, and check whether the application runs as expected.

.. note::

  For your executable to be compatible with gdb4hpc, it will need to be coded with MPI. You will also need to compile your code with the debugging flag ``-g`` (e.g. ``cc -g my_program.c -o my_exe``).
    
Launching through gdb4hpc
~~~~~~~~~~~~~~~~~~~~~~~~~

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
  
We will use ``launch`` to begin a multi-process application within gdb4hpc. Consider that we are wanting to test an application called ``my_exe``, and that we want this to be launched across all 256 processes in two nodes. We would launch this in gdb4hpc by running:

::

    dbg all> launch --launcher-args="--tasks-per-node=128 --cpus-per-task=1 --exclusive" $my_prog{256} ./my_ex
    
The default launcher is ``srun`` and the ``--launcher-args="..."`` allows you to set launcher flags for ``srun``. The variable ``$my_prog`` is a dummy name for the program being launched and you could use whatever name you want for it -- this will be the name of the ``srun`` job that will be run. The number in the brackets ``{256}`` is the number of processes over which the program will be executed, it's 256 here, but you could use any number. You should try to run this on as few processors as possible -- the more you use, the longer it will take for gdb4hpc to load the program.

Once the program is launched, gdb4hpc will load up the program and begin to run it. You will get output to screen something that looks like:

::

    Starting application, please wait...
    Creating MRNet communication network...
    Waiting for debug servers to attach to MRNet communications network...
    Timeout in 400 seconds. Please wait for the attach to complete.
    Number of dbgsrvs connected: [0];  Timeout Counter: [1]
    Number of dbgsrvs connected: [0];  Timeout Counter: [2]
    Number of dbgsrvs connected: [0];  Timeout Counter: [3]
    Number of dbgsrvs connected: [1];  Timeout Counter: [0]
    Number of dbgsrvs connected: [1];  Timeout Counter: [1]
    Number of dbgsrvs connected: [2];  Timeout Counter: [0]
    Finalizing setup...
    Launch complete.
    my_prog{0..255}: Initial breakpoint, main at /PATH/TO/my_program.c:34
    
The line number at which the initial breakpoint is made (in the above example, line 34) corresponds to the line number at which MPI is initialised. You will not be able to see any parts of the code outside of the MPI region of a code with gdb4hpc.

Once the code is loaded, you can use various commands to move through your code. The following lists and describes some of the most useful ones:

  * ``help`` -- Lists all gdb4hpc commands. You can run ``help COMMAND_NAME`` to learn more about a specific command (*e.g.* ``help launch`` will tell you about the launch command
  * ``list`` -- Will show the current line of code and the 9 lines following. Repeated use of ``list`` will move you down the code in ten-line chunks.
  * ``next`` -- Will jump to the next step in the program for each process and output which line of code each process is one. It will not enter subroutines. Note that there is no reverse-step in gdb4hpc.
  * ``step`` -- Like ``next``, but this will step into subroutines.
  * ``up`` -- Go up one level in the program (*e.g.* from a subroutine back to main).
  * ``print var`` -- Prints the value of variable ``var`` at this point in the code.
  * ``watch var`` -- Like print, but will print whenever a variable changes value.
  * ``quit`` -- Exits gdb4hpc.
  
Remember to exit the interactive session once you are done debugging.
    
Attaching with gdb4hpc
~~~~~~~~~~~~~~~~~~~~~~

Attaching to a hanging job using gdb4hpc is a great way of seeing which state each processor is in. However, this does not produce the most visually appealing results. For a more easy-to-read program, please take a look at `STAT`_

In your interactive session, launch your executable as a background task (by adding an ``&``  at the end of the command). For example, if you are running an executable called ``my_exe`` using 256 processes, you would run:

::

    srun -n 256 --nodes=2 --tasks-per-node=128 --cpus-per-task=1 --time=01:00:00 --account=[budget code] ./my_exe &
    
Make sure to replace the ``--account`` input to your budget code (*e.g.* if you are using budget t01, that part should look like ``--account=t01``).
    
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
    
This will output a large amount of information about this specific job. We are looking for the first number of this output, which should look like ``JOB_ID.##``  -- the number after the job ID is the number of slurm tasks performed in this interactive session. For our example (where ``srun`` is the first slurm task performed), the number is 1051.0.

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

After this, you will get an output that, among other things, tells you which line of your code each process is on, and what each process is doing. This can be helpful to see where the hang-up is.

If you accidentally attached to the wrong job, you can detach by running:

::

    dbg all> release $my_prog
    
and re-attach with the correct job ID. You will need to change your dummy name from ``$my_prog`` to something else.

When you are finished using ``gbd4hpc``, simply run:

::

  dbg all> quit
  
Do not forget to exit your interactive session.

valgrind4hpc
------------

Valgrind4hpc is a Valgrind-based debugging tool to aid in the detection of  memory  leaks  and  errors  in  parallel applications. Valgrind4hpc aggregates any duplicate messages  across  ranks  to  help  provide  an understandable picture of program behavior. Valgrind4hpc manages starting and redirecting output from many copies of  Valgrind,  as  well  as recombining  and filtering Valgrind messages.  If your program can be debugged with Valgrind, it can be debugged with valgrind4hpc.

The valgrind4hpc module enables the use of standard valgrind as well as the valgrind4hpc version more suitable to parallel programs.


.. warning::

  There is a known issue with `valgrind4hpc`: the compiler wrappers (ftn, cc, CC) do not work while this module is loaded. To compile, you will need to unload the module (`module unload valgrind4hpc`), compile, and reload the module (`module load valrgind4hpc`).

Using valgrind
~~~~~~~~~~~~~~

First, load ``valgrind4hpc``:

::

    module load valgrind4hpc
    
Next, run your executable through valgrind:

::

    valgrind --tool=memcheck --leak-check=yes my_executable
    
The log outputs to screen. The `ERROR SUMMARY` will tell you whether, and how many, memory errors there are in your script. Furthermore, if you compile your code using the ``-g`` debugging flag (*e.g.* ``gcc -g my_progam.c -o my_executable.c``), the log will point out the code lines where the error occurs.

Valgrind also includes a tool called Massif that can be used to give insight into the memory usage of your program. It takes regular snapshots and outputs this data into a single file, which can be visualised to show the total amount of memory used as a function of time. This shows when peaks and bottlenecks occur and allows you to identify which data structures in your code are responsible for the largest memory usage of your program.

Documentation explaining how to use Massif is available at the `official Massif manual<https://www.valgrind.org/docs/manual/ms-manual.html>`_. In short, you should run your executable as follows:

::

    valgrind --tool=massif my_executable
    
he memory profiling data will be output into a file called ``massif.out.pid``, where pid is the runtime process ID of your program. A custom filename can be chosen using the ``--massif-out-file option``, as follows:

::

    valgrind --tool=massif --massif-out-file=optional_filename.out my_executable

The output file contains raw profiling statistics. To view a summary including a graphical plot of memory usage over time, use the ``ms_print`` command as follows:

::

    ms_print massif.out.12345

or, to save to a file:

::

    ms_print massif.out.12345 > massif.analysis.12345

This will show total memory usage over time as well as a breakdown of the top data structures contributing to memory usage at each snapshot where there has been a significant allocation or deallocation of memory. 

Using valgrind4hpc
~~~~~~~~~~~~~~~~~~

First, load ``valgrind4hpc``:

::

    module load valgrind4hpc
    
Valgrind4hpc will launch an srun job to run the executable while it profiles. To test an executable called ``my_executable`` that requires two arguments ``arg1`` and ``arg2`` on two nodes and 256 processes, run:

::

    valgrind4hpc --tool=memcheck --num-ranks=256 --launcher-args="--exclusive --ntasks-per-node=128 --cpus-per-task=1" my_executable -- arg1 arg2
    
In particular, note the ``--`` separating the executable from the arguments (this is not necessary of your executable takes no arguments). The ``--lancher-args="arguments"`` allow you to set launcher flags for ``srun``.

Valgrind4hpc only supports certain tools found in valgrind. These are: memcheck, helgrind, exp-sgcheck, or drd. The ``--valgrind-args="arguments"`` allows users to use valgrind options not supported in valgrind4hpc (*e.g.* ``--leak-check``) -- note, however, that some of these options might interfere with valgrind4hpc.

More information on valgrind4hpc can be found in the manual (``man valgrind4hpc``). 
    
STAT
----

The Stack Trace Analysis Tool (STAT) is a cross-platform debugging tool from the University of Wisconsin-Madison. ATP is based on the same technology as STAT, both are designed to gather and merge stack traces from a running application's parallel processes. The STAT tool can be useful when application seems to be deadlocked or stuck, i.e. they don't crash but they don't progress as expected, and it has been designed to scale to a very large number of processes. Full information on STAT, including use cases, is available at the `STAT website <https://hpc.llnl.gov/software/development-environment-software/stat-stack-trace-analysis-tool>`_.

STAT will attach to a running program and query that program to find out where all the processes in that program currently are. It will then process that data and produce a graph displaying the unique process locations (i.e. where all the processes in the running program currently are). To make this easily understandable it collates together all processes that are in the same place providing only unique program locations for display. 

Using STAT on ARCHER2
~~~~~~~~~~~~~~~~~~~~~

On the login node, load the ``cray-stat`` module:

::

    module load cray-stat
    
Then, launch your job using ``srun`` as a background task (by adding an ``&`` at the end of the command). For example, if you are running an executable called ``my_exe`` using 256 processes, you would run:

::

    srun -n=256 --nodes=2 --tasks-per-node=128 --cpus-per-task=1 --time=01:00:00 --account=[budget code] ./my_exe &
    
Note that this example has set the job time limit to 1 hour -- if you need longer, change the ``--time`` command.

You will need the Program ID (PID) of the job you have just launched -- the PID is printed to screen upon launch, or you can get it by running:

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

Once your application has reached the point where it hangs, issue the following command (replacing PID with the ID of the **first** srun task -- in the above example, I would replace PID with 157183):

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

Once STAT is finished, you can kill the srun job using ``scancel`` (replacing JID with the job ID of the job you just launched):

::
    
    scancel JID
    
You can view the results that STAT has produced using the following command (note that "my_exe" will need to be replaced with the name of the executable you ran):

::

    stat-view stat_results/my_exe.0000/00_my_exe.0000.3D.dot
    
This produces a graph displaying all the different places within the program that the parallel processes were when you queried them.

.. note::

  To see the graph, you will need to have exported your X display when logging in.

ATP
---
  
To enable ATP you should load the atp module and set the "ATP_ENABLED" environment variable to 1 on the login node:

::

    module load atp
    export ATP_ENABLED=1
    
Then, launch your job using ``srun`` as a background task (by adding an ``&`` at the end of the command). For example, if you are running an executable called ``my_exe`` using 256 processes, you would run:

::

    srun -n=256 --nodes=2 --tasks-per-node=128 --cpus-per-task=1 --time=01:00:00 --account=[budget code] ./my_exe &
    
 Note that this example has set the job time limit to 1 hour -- if you need longer, change the ``--time`` command.
 
 Once the job has finished running, load the ``stat`` module to view the results:
 
 ::
 
     module load cray-stat
     
and view the merged stack trace using:

::

    stat-view atpMergedBT.dot
    
.. note::
  
  To see the graph, you will need to have exported your X display when logging in.

.. CCDB
.. ----
.. 
.. #####################
.. Some notes & things to do
.. * CCDB is not currently working -- will need to be added once it's fixed
.. * Will need to test various programs once accounts, partitions, and qos's are implemented 

