Debugging
=========

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

Debugging applications on ARCHER2.

STAT
----

The Stack Trace Analysis Tool (STAT) is a cross-platform debugging tool from the University of Wisconsin-Madison. ATP is based on the same technology as STAT, both are designed to gather and merge stack traces from a running application's parallel processes. The STAT tool can be useful when application seems to be deadlocked or stuck, i.e. they don't crash but they don't progress as expected, and it has been designed to scale to a very large number of processes. Full information on STAT, including use cases, is available at the STAT website.

STAT will attach to a running program and query that program to find out where all the processes in that program currently are. It will then process that data and produce a graph displaying the unique process locations (i.e. where all the processes in the running program currently are). To make this easily understandable it collates together all processes that are in the same place providing only unique program locations for display. 

Using STAT on ARCHER2
~~~~~~~~~~~~~~~~~~~~~
To use the stat tool, you need to request an allocation session:

::

    salloc --nodes=2 --tasks-per-node=128 --cpus-per-task=1 --time=01:00:00 --account=[budget code]
    
You will know your session has launched when getting the message:

::

    salloc: Granted job allocation 925
    
Once you've launched your interactive session and navigated to the `/work` directory where you will run your code. You will need to load the `cray-stat` module:

::

    module load cray-stat
    
Then, launch your job as normal, but as a background task (by adding an `&` at the end of the command). For example, if you are running an executable called my_exe using 256 processes, you would run:

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
