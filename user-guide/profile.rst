Profiling 
==========

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


3. Compile your code in the standard way. Object files need to be made available to CrayPat to correctly build an instrumented executable for profiling or tracing, this means that compile and link stage should be separated by using the ``-c`` compile flag. 

::
   
 [user@archer2]$ cc -h std=c99 -c myapp.c
 [user@archer2]$ cc myapplication.o -o myapp.x 

4. Instrument your application
   To instrument then the binary, run the ``pat_build`` command. This will generate a new binary with ``+pat`` appended to the end (e.g. ``myapp.x+pat``)

::
 
   [user@archer2]$ pat_build myapplication.x


5. Run the new executable with ``+pat`` appended as you would with the regular executable. This will generate a performance data file with the suffix ``.xf`` (e.g. ``myapp+pat+15571-2838s.xf``).
   
6. Generate report data
   
This ``.xt`` file contains the raw sampling data from the run and needs to be post processed to produce useful results. This is done using the ``pat_report`` tool which converts all the raw data into a summarised and readable form.

::

   
   [user@archer2]$ pat_report myapp+pat+15571-2838s.xf > myreport.txt

This report will generate two more files, one with the extension ``.ap2`` which holds the same data as the ``.xf`` but in the post processed form. The other file has a ``.apa`` extension and is a text file with a suggested configuration for generating a traced experiment. The ``.ap2`` file generated is used to view performance data graphically with the Cray Apprentice2 tool, and the latter is used for more detailed tracing experiments. 
 

Tracing analysis
^^^^^^^^^^^^^^^^
We can produce a focused tracing experiment based on the results from the *sampling* experiment using ``pat_build`` with the ``.apa`` file produced during the sampling.

::

    [user@archer2]$ pat_build -O myapp+pat+15571-2838s.apa

This will produce a third binary with extension ``+apa``. This binary should once again be run on the compute nodes and the name of the executable changed to ``myapp+apa``. 

Further help
^^^^^^^^^^^^
* `CrayPat User Guide <https://pubs.cray.com/content/S-2376/7.0.0/cray-performance-measurement-and-analysis-tools-user-guide/craypat>`__
