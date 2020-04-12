Profiling and performance tuning
================================

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
   
 [user@archer2]$ ftn -h std=c99  -o test mpi_test.c
 INFO: creating the CrayPat-instrumented executable 'mpi_test' (lite-samples) ...OK  

4. Run the generated executable normally submitting a job.

After the job finishes executing, CrayPat-lite output should be printed to stdout i.e. at the end of the job's output file generated. A new directory will also be created in the directory the run occurred in with ``.rpt`` and ``.ap2`` files. The ``.rpt`` files are text files that contain the same information printed in the job's output file, the ``.ap2`` files can be used to obtained more detailed information  and can be visualized with the Cray Apprentice2 tool.

Further help
^^^^^^^^^^^^
* `CrayPat-lite User Guide <https://pubs.cray.com/content/S-2376/7.0.0/cray-performance-measurement-and-analysis-tools-user-guide/craypat-lite>`__



CrayPat
-------

