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

   module list

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

   #SBATCH --name=craypat_test
   #SBATCH --nodes=4
   #SBATCH --ntasks=512
   #SBATCH --tasks-per-node=128
   #SBATCH --cpus-per-core=1
   #SBATCH --time=00:20:00
   
   #SBATCH --account=[budget code]
   
   srun mpi_test.x

5. Analyse the data
   
After the job finishes executing, CrayPat-lite output should be printed to stdout i.e. at the end of the job's output file generated. A new directory will also be created in the directory the run occurred in with ``.rpt`` and ``.ap2`` files. The ``.rpt`` files are text files that contain the same information printed in the job's output file, the ``.ap2`` files can be used to obtained more detailed information  and can be visualized with the Cray Apprentice2 tool.

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

   module list

2. Load ``perfotools`` module

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

   
   [user@archer2]$ pat_report jacobi+pat+15571-2838s.xf 
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
 
 

This report will generate two more files, one with the extension ``.ap2`` which holds the same data as the ``.xf`` but in the post processed form. The other file has a ``.apa`` extension and is a text file with a suggested configuration for generating a traced experiment. The ``.ap2`` file generated is used to view performance data graphically with the Cray Apprentice2 tool, and the latter is used for more detailed tracing experiments. 
 

Tracing analysis
^^^^^^^^^^^^^^^^
Automatic Program Analysis (APA)¶
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

Using ``-g`` a program can be instrumented to trace all function entry point references belonging to the trace function group tracegroup (mpi, libsci, lapack, scalapack, heap, etc).a

::

   [user@archer2]$ pat_build -w	-g mpi jacobi

Further help
^^^^^^^^^^^^
* `CrayPat User Guide <https://pubs.cray.com/content/S-2376/7.0.0/cray-performance-measurement-and-analysis-tools-user-guide/craypat>`__
