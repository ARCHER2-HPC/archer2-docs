Profiling and performance tuning
================================

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

Profiling and understanding performance on ARCHER2 including performance tuning advice.

MPI
---

The vast majority of parallel scientific software uses the MPI library
as the main way to implement parallelism; it is used so universally
that the Cray compiler wrappers on ARCHER2 link to the Cray MPI
library by default. Unlike other clusters you may have used, there is
no choice of MPI library on ARCHER2: regardless of what compiler you
are using, your program will use Cray MPI. This is because the
Slingshot network on ARCHER2 is Cray-specific and significant effort
has been put in by Cray software engineers to optimise the MPI
performance on Cray Shasta systems.

Here we list a number of suggestions for improving the performance of
your MPI programs on ARCHER2. Although MPI programs are capable of
scaling very well due to the bespoke communications hardware and
software, the details of how a program calls MPI can have significant
effects on achieved performance.

.. note::

  Many of these tips are actually quite generic and should be
  beneficial to any MPI program; however, they all become much more
  important when running on very large numbers of processes on a
  machine the size of ARCHER2.

Collective operations
~~~~~~~~~~~~~~~~~~~~~

Many of the collective operations that are commonly required by
parallel scientific programs, i.e. operations that involve a group of
processes, are already implemented in MPI. The canonical operation is
perhaps adding up a double precision number across all MPI processes
which is best achieved by a *reduction operation*::

  MPI_Allreduce(&x, &xsum, 1, MPI_DOUBLE, MPI_SUM, MPI_COMM_WORLD);


  
Parallel IO
~~~~~~~~~~~
