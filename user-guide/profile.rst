Profiling 
==========

.. highlight:: c

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.


CrayPat-lite
------------
CrayPat-lite is a simplified and easy-to-use version of the Cray Performance Measurement and Analysis Tool (CrayPat) set. CrayPat-lite provides basic performance analysis information automatically, with a minimum of user interaction, and yet offers information useful to users wishing to explore a program's behavior further using the full CrayPat tool set.

To use CrayPat-lite you only need to make sure that the base CrayPat perftools-base module has been loaded, an instrumentation module can then be loaded for further experimentation.

How to use CrayPat-lite
^^^^^^^^^^^^^^^^^^^^^^^
1. Ensure the ``perftools-base`` module is loaded

::

   module load perftools-base

2. Load ``perfotools-lite`` module

::

   module load perftools-lite

3. Compile your application normally. An information message from CrayPat-lite will appear indicating that the executable has been instrumented.

::
   
 [user@archer2]$ cc -h std=c99  -o myapplication.x myapplication.c
 INFO: creating the CrayPat-instrumented executable 'myapplication.x' (lite-samples) ...OK  

4. Run the generated executable normally submitting a job.

.. warning::

  The following SLURM script is provisional and should be verified

::

   #!/bin/bash

   #SBATCH --job-name=craypat_test
   #SBATCH --nodes=4
   #SBATCH --tasks-per-node=128
   #SBATCH --cpus-per-task=1
   #SBATCH --time=00:20:00
   
   #SBATCH --account=[budget code]
   
   srun mpi_test.x

5. Analyse the data
   
After the job finishes executing, CrayPat-lite output should be printed to stdout i.e. at the end of the job's output file generated. A new directory will also be created in the directory the run occurred in with ``.rpt`` and ``.ap2`` files. The ``.rpt`` files are text files that contain the same information printed in the job's output file, the ``.ap2`` files can be used to obtained more detailed information  and can be visualized with the Cray Apprentice2 tool (for information on using this, please take a look at `Cray Apprentice2`_).

Further help
^^^^^^^^^^^^
* `CrayPat-lite User Guide <https://pubs.cray.com/content/S-2376/7.0.0/cray-performance-measurement-and-analysis-tools-user-guide/craypat-lite>`__



CrayPat
-------
The Cray Performance Analysis Tool (CrayPAT) is a powerful framework for analysing a parallel
application’s performance on Cray supercomputers. It can provide very detailed information on
the timing and performance of individual application procedures.

CrayPat can perform two types of performance analysis: *sampling* experiments and *tracing* experiments. A sampling experiment probes the code at a predefined interval and produces a report based on these statistics. A tracing experiment explicitly monitors the code performance within named routines. Typically, the overhead associated with a tracing experiment is higher than that associated with a sampling experiment but provides much more detailed information. The key to getting useful data out of a sampling experiment is to run your profiling for a representative length of time.

Sampling analysis
^^^^^^^^^^^^^^^^^


1. Ensure the ``perftools-base`` module is loaded

::

   module load perftools-base

2. Load ``perftools`` module

::

   module load perftools


3. Compile your code in the standard way always using the Cray compiler wrappers (ftn, cc and CC). Object files need to be made available to CrayPat to correctly build an instrumented executable for profiling or tracing, this means that compile and link stage should be separated by using the ``-c`` compile flag. 

::
   
 [user@archer2]$ cc -h std=c99 -c jacobi.c
 [user@archer2]$ cc jacobi.o -o jacobi 



4. Instrument your application
   To instrument then the binary, run the ``pat_build`` command. This will generate a new binary with ``+pat`` appended to the end (e.g. ``jacobi+pat``)

::
 
   [user@archer2]$ pat_build jacobi


5. Run the new executable with ``+pat`` appended as you would with the regular executable. This will generate performance data files with the suffix ``.xf`` (e.g. ``jacobi+pat+12265-1573s/xf-files``).


6. Generate report data
   
This ``.xt`` file contains the raw sampling data from the run and needs to be post processed to produce useful results. This is done using the ``pat_report`` tool which converts all the raw data into a summarised and readable form.

::

   
   [user@archer2]$ pat_report jacobi+pat+12265-1573s
   
   Table 1:  Profile by Function (limited entries shown)

   Samp% |  Samp |  Imb. |  Imb. | Group
         |       |  Samp | Samp% |  Function
         |       |       |       |   PE=HIDE
  100.0% | 849.5 |    -- |    -- | Total
 |--------------------------------------------------
 |  56.7% | 481.4 |    -- |    -- | MPI
 ||-------------------------------------------------
 ||  48.7% | 414.1 |  50.9 | 11.0% | MPI_Allreduce
 ||   4.4% |  37.5 | 118.5 | 76.6% | MPI_Waitall
 ||   3.0% |  25.2 |  44.8 | 64.5% | MPI_Isend
 ||=================================================
 |  29.9% | 253.9 |  55.1 | 18.0% | USER
 ||-------------------------------------------------
 ||  29.9% | 253.9 |  55.1 | 18.0% | main
 ||=================================================
 |  13.4% | 114.1 |    -- |    -- | ETC
 ||-------------------------------------------------
 ||  13.4% | 113.9 |  26.1 | 18.8% | __cray_memcpy_SNB
 |==================================================
 
 

This report will generate two more files, one with the extension ``.ap2`` which holds the same data as the ``.xf`` but in the post processed form. The other file has a ``.apa`` extension and is a text file with a suggested configuration for generating a traced experiment. The ``.ap2`` file generated is used to view performance data graphically with the Cray Apprentice2 tool (for information on using this, please take a look at `Cray Apprentice2`_), and the latter is used for more detailed tracing experiments. 

The ``pat_report`` command is able to produce many different profile reports from the profile data. You can select a predefined report with the ``-O`` flag to ``pat_report``. A selection of the most generally useful predefined report types are

* **ca+src** - Show the callers (bottom-up view) leading to the routines that have a high use in the report and include source code line numbers for the calls and time-consuming statements.
* **load_balance** - Show load-balance statistics for the high-use routines in the program. Parallel processes with minimum, maximum and median times for routines will be displayed. Only available with tracing experiments.
* **mpi_callers** - Show MPI message statistics. Only available with tracing experiments.


::

   [user@archer2]$ pat_report -O ca+src,load_balance  jacobi+pat+12265-1573s
   
   Table 1:  Profile by Function and Callers, with Line Numbers (limited entries shown)

   Samp% |  Samp |  Imb. |  Imb. | Group
         |       |  Samp | Samp% |  Function
         |       |       |       |   PE=HIDE
  100.0% | 849.5 |    -- |    -- | Total
 |--------------------------------------------------
 |--------------------------------------
 |  56.7% | 481.4 | MPI
 ||-------------------------------------
 ||  48.7% | 414.1 | MPI_Allreduce
 3|        |       |  main:jacobi.c:line.80
 ||   4.4% |  37.5 | MPI_Waitall
 3|        |       |  main:jacobi.c:line.73
 ||   3.0% |  25.2 | MPI_Isend
 |||------------------------------------
 3||   1.6% |  13.2 | main:jacobi.c:line.65
 3||   1.4% |  12.0 | main:jacobi.c:line.69
 ||=====================================
 |  29.9% | 253.9 | USER
 ||-------------------------------------
 ||  29.9% | 253.9 | main
 |||------------------------------------
 3||  18.7% | 159.0 | main:jacobi.c:line.76
 3||   9.1% |  76.9 | main:jacobi.c:line.84
 |||====================================
 ||=====================================
 |  13.4% | 114.1 | ETC
 ||-------------------------------------
 ||  13.4% | 113.9 | __cray_memcpy_SNB
 3|        |       |  __cray_memcpy_SNB
 |======================================

   
Tracing analysis
^^^^^^^^^^^^^^^^
Automatic Program Analysis (APA)
"""""""""""""""""""""""""""""""""
We can produce a focused tracing experiment based on the results from the *sampling* experiment using ``pat_build`` with the ``.apa`` file produced during the sampling.

::

    [user@archer2]$ pat_build -O jacobi+pat+12265-1573s/build-options.apa
    

This will produce a third binary with extension ``+apa``. This binary should once again be run on the compute nodes and the name of the executable changed to ``jacobi+apa``. As with the sampling analysis, a report can be produced using ``pat_report``.

::

   [user@archer2]$ pat_report jacobi+apa+13955-1573t
   
   Table 1:  Profile by Function Group and Function (limited entries shown)

   Time% |      Time |     Imb. |  Imb. |       Calls | Group
         |           |     Time | Time% |             |  Function
         |           |          |       |             |   PE=HIDE

  100.0% | 12.987762 |       -- |    -- | 1,387,544.9 | Total
 |-------------------------------------------------------------------------
 |  44.9% |  5.831320 |       -- |    -- |         2.0 | USER
 ||------------------------------------------------------------------------
 ||  44.9% |  5.831229 | 0.398671 |  6.4% |         1.0 | main
 ||========================================================================
 |  29.2% |  3.789904 |       -- |    -- |   199,111.0 | MPI_SYNC
 ||------------------------------------------------------------------------
 ||  29.2% |  3.789115 | 1.792050 | 47.3% |   199,109.0 | MPI_Allreduce(sync)
 ||========================================================================
 |  25.9% |  3.366537 |       -- |    -- | 1,188,431.9 | MPI
 ||------------------------------------------------------------------------
 ||  18.0% |  2.334765 | 0.164646 |  6.6% |   199,109.0 | MPI_Allreduce
 ||   3.7% |  0.486714 | 0.882654 | 65.0% |   199,108.0 | MPI_Waitall
 ||   3.3% |  0.428731 | 0.557342 | 57.0% |   395,104.9 | MPI_Isend
 |=========================================================================

Manual Program Analysis
"""""""""""""""""""""""

CrayPat allows you to manually choose your profiling preference. This is particularly useful if the APA mode does not meet your tracing analysis requirements.

The entire program can be traced as a whole using ``-w``:

::

   [user@archer2]$ pat_build -w jacobi

Using ``-g`` a program can be instrumented to trace all function entry point references belonging to the trace function group tracegroup (mpi, libsci, lapack, scalapack, heap, etc)

::

   [user@archer2]$ pat_build -w	-g mpi jacobi


Dynamically-linked binaries
^^^^^^^^^^^^^^^^^^^^^^^^^^^
CrayPat allows you to profile un-instrumented, dynamically linked binaries with the ``pat_run`` utility. ``pat_run`` delivers profiling information for codes that cannot easily be rebuilt.
To use ``pat_run``:

1. Load the ``perfotools-base`` module if it is not already loaded

::

   module load perftools-base

2. Run your application normally including the ``pat_run`` command rigth after your ``srun`` options

::

    srun [srun-options] pat_run [pat_run-options] program [program-options]

3. Use ``pat_report`` to examine any data collected during the execution of your application.

::

   [user@archer2]$ pat_report jacobi+pat+12265-1573s 

Some useful ``pat_run`` options are:


``-w``
    Collect data by tracing.
``-g``
    Trace functions belonging to group names. See the -g option in pat_build(1) for a list of valid tracegroup values.
``-r``
    Generate a text report upon successful execution.


Further help
^^^^^^^^^^^^
* `CrayPat User Guide <https://pubs.cray.com/content/S-2376/7.0.0/cray-performance-measurement-and-analysis-tools-user-guide/craypat>`__

  
    
Cray Apprentice2
----------------
Cray Apprentice2 is an optional GUI tool that is used to visualize and manipulate the performance analysis data captured during program execution. Cray Apprentice2 can be run either on the Cray system or, optionally, on a standalone Linux desktop machine. Cray Apprentice2 can display a wide variety of reports and graphs, depending on the type of program being analyzed, the way in which the program was instrumented for data capture, and the data that was collected during program execution.

You will need to use CrayPat first, to instrument your program and capture performance analysis data, and then use Cray Apprentice2 to visualize and explore the resulting data files.

The number and appearance of the reports that can be generated using Cray Apprentice2 is determined by the kind and quantity of data captured during program execution, which in turn is determined by the way in which the program was instrumented and the environment variables in effect at the time of program execution. For example, changing the PAT_RT_SUMMARY environment variable to 0 before executing the instrumented program nearly doubles the number of reports available when analyzing the resulting data in Cray Apprentice2.

::

   export PAT_RT_SUMMARY=0


To use Cray Apprentice2 (``app2``), load ``perftools-base`` module if it is not already loaded

::

   module load perftools-base
   
then open the Cray Apprentice2 data (``.ap2``) generated during the instrumentation phase

::

   [user@archer2]$ app2 jacobi+pat+12265-1573s/datafile.ap2

    
   
Hardware Performance Counters
-----------------------------

