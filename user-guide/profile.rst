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

Synchronous vs asynchronous communications
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MPI_Send
********

A standard way to send data in MPI is using ``MPI_Send`` (aptly called
*standard send*). MPI has the choice to implement this:

Synchronously
   The sending process waits until a matching receive has
   been posted, i.e. it operates like ``MPI_Ssend``. This clearly has
   the risk of deadlock if no receive is ever issued.

Asynchronously MPI makes a copy of the message into an internal buffer
  and returns immediately without waiting for a matching receive; the
  message may actually be delivered later on. This is like the
  behaviour of the the buffered send routine ``MPI_Bsend``.
  
The rationale is that MPI should decided how best to send a
message.

In practice, what typically happens is that MPI tries to use an
asynchronous approach via the *eager* protocol -- the message is
copied directly to a preallocated buffer *on the receiver* and the
routine returns immediately afterward. Clearly there is a limit on how
much space can be reserved for this, so small messages will be sent
asynchronously whereas larger messages are sent synchronously.

The threshold is often termed the *eager limit* which is fixed for the
entire run of your program. It will have some default setting which
varies from system to system, but might be around 8K bytes.

Implications
************

- You should **never assume** that ``MPI_Send`` buffers your message,
  so if you have concerns about deadlock you will need to use the
  non-blocking variant ``MPI_Isend`` to guarantee that the send routine returns control to you immediately even if there is no matching receive.

- It is **not enough** to say *deadlock is an issue in principle, but
  it runs OK on my laptop so there is no problem in practice*. The
  *eager limit* is system-dependent so the fact that a message happens
  to be buffered on your laptop is no guarantee it will be buffered on
  ARCHER2.

- To check that you have a correct code, replace all instances of
  ``MPI_Send`` / ``MPI_Isend`` with ``MPI_Ssend`` / ``MPI_Issend``. A
  correct MPI program should still run correctly when all references to
  standard send are replaced by synchronous send (since MPI is allowed
  to implement standard send as synchronous send).

- However, an MPI program will typically run *faster* if ``MPI_Send``
  is implemented using the eager protocol since synchronisation
  between sender and receive is much reduced.
  
Tuning performance
******************

With most MPI libraries you should be able to alter the default value
of the eager limit at runtime, perhaps via an environment variable or
a command-line argument to ``mpirun``. On ARCHER, the magic
incantation is::

  export MPICH_GNI_MAX_EAGER_MSG_SIZE=16382

which would mean that messages below 16K bytes are sent
asynchronously (there will be a similar, but different, incantation on
ARCHER2).

The advice for tuning the performance of ``MPI_Send`` is

- find out what the distribution of message sizes for ``MPI_Send`` is
  (a profiling tool may be useful here);

- this applies to ``MPI_Isend`` as well: even in the non-blocking
  form, which can help to weaken synchronisation between sender and
  receiver, the amount of hand-shaking required is much reduced if the
  eager protocol is used;

- find out from the system documentation how to alter the value of the
  eager limit (there is no standardised way to set it);

- set the eager limit to a value larger than your typical message size
  -- you may need to add a small amount, say a few hundred bytes, to
  allow for any additional header information that is added to each
  message;

- measure the performance before and after to check that it has improved.

.. note::

   It cannot be stressed strongly enough that although the performance
   may be affected by the value of the eager limit, the functionality
   of your program should be unaffected. If changing the eager limit
   affects the correctness of your program (e.g. whether or not it
   deadlocks) then **you have an incorrect MPI Program**.

Collective operations
~~~~~~~~~~~~~~~~~~~~~

Many of the collective operations that are commonly required by
parallel scientific programs, i.e. operations that involve a group of
processes, are already implemented in MPI. The canonical operation is
perhaps adding up a double precision number across all MPI processes,
which is best achieved by a *reduction operation*::

  MPI_Allreduce(&x, &xsum, 1, MPI_DOUBLE, MPI_SUM, MPI_COMM_WORLD);

This will be implemented using an efficient algorithm, for example
based on a binary tree. Using such *divide-and-conquer* approaches
typically results in an algorithm whose execution time on :math:`P`
processes scales as :math:`log_2(P)`; compare this to a naive approach
where every process sends its input to rank 0 where the time will
scale as :math:`P`. This might not be significant on your laptop, but
even on as few as 1000 processes the tree-based algorithm will already
be around 100 times faster.

So, the basic advice is *always use a collective routine to implement
your communications pattern* if at all possible.

Sometimes, the collective routines available may not appear to do
exactly what you want. However, they can sometimes be used with a
small amount of additional programming work:

- To operate on a subset of processes, create sub-communicators
  containing the relevant subset(s) and use these communicators
  instead of ``MPI_COMM_WORLD``. Useful functions for communicator
  management include:

  - ``MPI_Comm_split`` is the most general routine;
  - ``MPI_Comm_split_type`` can be used to create a separate communicator for each shared-memory node with ``split type = MPI_COMM_TYPE_SHARED``;
  - ``MPI_Cart_sub`` can divide a Cartesian communicator into regular slices.

- If the communication pattern is what you want, but the data on each
  process is not arranged in the required layout, consider using MPI
  derived data types for the input and/or output. This can be useful,
  for example, if you want to communicate non-contiguous data such as
  a subsection of a multidimensional array although care must be taken
  in defining these types to ensure they have the correct extents.

  Another example would be using ``MPI_Allreduce`` to add up an
  integer and a double-precision variable using a single call by
  putting them together into a C ``struct`` and defining a matching
  MPI datatype using ``MPI_Type_create_struct``. Here you would also
  have to provide MPI with a custom reduction operation using
  ``MPI_Op_create``.
  
Many MPI programs call ``MPI_Barrier`` to explicitly synchronise all
the processes. Although this can be useful for getting reliable
performance timings, it is rare in practice to find a program where
the call is actually needed for correctness. For example, you may
see::

    // Ensure the input x is available on all processes
    MPI_Barrier(MPI_COMM_WORLD);
    // Perform a global reduction operation
    MPI_Allreduce(&x, &xsum, 1, MPI_DOUBLE, MPI_SUM, MPI_COMM_WORLD);
    // Ensure the result xsum is available on all processes
    MPI_Barrier(MPI_COMM_WORLD);


**Neither of these barriers are needed** as the reduction operation
performs all the required synchronisation.
   

.. note::

   If you run a performance profiler on your code and it shows a lot
   of time being spent in a collective operation such as
   ``MPI_Allreduce``, this is *not necessarily* a sign that the
   reduction operation itself is the bottleneck. This is often a
   symptom of *load imbalance*: even if a reduction operation is
   efficiently implemented, it may take a long time to complete if the
   MPI processes do not all call it at the same
   time. ``MPI_Allreduce`` synchronises across processes so will have
   to wait for all the processes to call it before it can complete. A
   single slow process will therefore adversely impact the performance
   of your entire parallel program.
   


.. Parallel IO
.. ~~~~~~~~~~~
