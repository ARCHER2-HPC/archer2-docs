# Performance tuning

## MPI

The vast majority of parallel scientific applications use the MPI
library as the main way to implement parallelism; it is used so
universally that the Cray compiler wrappers on ARCHER2 link to the
Cray MPI library by default. Unlike other clusters you may have used,
there is no choice of MPI library on ARCHER2: regardless of what
compiler you are using, your program will use Cray MPI. This is
because the Slingshot network on ARCHER2 is Cray-specific and
significant effort has been put in by Cray software engineers to
optimise the MPI performance on their Shasta systems.

Here we list a number of suggestions for improving the performance of
your MPI programs on ARCHER2. Although MPI programs are capable of
scaling very well due to the bespoke communications hardware and
software, the details of how a program calls MPI can have significant
effects on achieved performance.

!!! note
    Many of these tips are actually quite generic and should be beneficial
    to any MPI program; however, they all become much more important when
    running on very large numbers of processes on a machine the size of
    ARCHER2.

### Synchronous vs asynchronous communications

#### MPI\_Send

A standard way to send data in MPI is using `MPI_Send` (aptly called
*standard send*). Somewhat confusingly, MPI is allowed to choose how to
implement this in two different ways:

  - Synchronously  
    The sending process waits until a matching receive has been posted,
    i.e. it operates like `MPI_Ssend`. This clearly has the risk of
    deadlock if no receive is ever issued.

  - Asynchronously  
    MPI makes a copy of the message into an internal buffer and returns
    straight away without waiting for a matching receive; the message
    may actually be delivered later on. This is like the behaviour of
    the the buffered send routine `MPI_Bsend`.

The rationale is that MPI, rather than the user, should decide how best
to send a message.

In practice, what typically happens is that MPI tries to use an
asynchronous approach via the *eager* protocol: the message is sent
directly to a preallocated buffer *on the receiver* and the routine
returns immediately afterwards. Clearly there is a limit on how much
space can be reserved for this, so:

  - **small messages** will be sent asynchronously;
  - **large messages** will be sent synchronously.

The threshold is often termed the *eager limit* which is fixed for the
entire run of your program. It will have some default setting which
varies from system to system, but might be around 8K bytes.

#### Implications

  - An MPI program will typically run *faster* if `MPI_Send` is
    implemented asynchronously using the eager protocol since
    synchronisation between sender and receive is much reduced.
  - However, you should **never assume** that `MPI_Send` buffers your
    message, so if you have concerns about deadlock you will need to use
    the non-blocking variant `MPI_Isend` to guarantee that the send
    routine returns control to you immediately even if there is no
    matching receive.
  - It is **not enough** to say *deadlock is an issue in principle, but
    it runs OK on my laptop so there is no problem in practice*. The
    *eager limit* is system-dependent so the fact that a message happens
    to be buffered on your laptop is no guarantee it will be buffered on
    ARCHER2.
  - To check that you have a correct code, replace all instances of
    `MPI_Send` / `MPI_Isend` with `MPI_Ssend` / `MPI_Issend`. A correct
    MPI program should still run correctly when all references to
    standard send are replaced by synchronous send (since MPI is allowed
    to implement standard send as synchronous send).

#### Tuning performance

With most MPI libraries you should be able to alter the default value of
the eager limit at runtime, perhaps via an environment variable or a
command-line argument to `mpirun`.

The advice for tuning the performance of `MPI_Send` is

  - find out what the distribution of message sizes for `MPI_Send` is (a
    profiling tool may be useful here);
  - this applies to `MPI_Isend` as well: even in the non-blocking form,
    which can help to weaken synchronisation between sender and
    receiver, the amount of hand-shaking required is much reduced if the
    eager protocol is used;
  - find out from the system documentation how to alter the value of the
    eager limit (there is no standardised way to set it);
  - set the eager limit to a value larger than your typical message size
    -- you may need to add a small amount, say a few hundred bytes, to
    allow for any additional header information that is added to each
    message;
  - measure the performance before and after to check that it has
    improved.

!!! note
    It cannot be stressed strongly enough that although the performance may
    be affected by the value of the eager limit, the functionality of your
    program should be unaffected. If changing the eager limit affects the
    correctness of your program (e.g. whether or not it deadlocks) then
    **you have an incorrect MPI program**.

#### Setting the eager limit on ARCHER2

On ARCHER2, things are a little more complicated. Although the eager
limit defaults to 16KiB, messages up to 256KiB are sent
asynchronously because they are actually sent as a number of smaller
messages.

To send even larger messages asynchronously, alter the value of
`FI_OFI_RXM_SAR_LIMIT` in your job submission script, e.g. to set to 512KiB:

    export FI_OFI_RXM_SAR_LIMIT=524288

You can also control the size of the smaller messages by altering the value
of `FI_OFI_RXM_BUFFER_SIZE`
in your job submission script, e.g. to set to 128KiB:

    export FI_OFI_RXM_BUFFER_SIZE=131072

A different protocol is used for messages between two processes on the
same node. The default eager limit for these is 8K. Although the
performance of on-node messages is unlikely to be a limiting factor
for your program you can change this value, e.g. to set to 16KiB:

    export MPICH_SMP_SINGLE_COPY_SIZE=16384

### Collective operations

Many of the collective operations that are commonly required by parallel
scientific programs, i.e. operations that involve a group of processes,
are already implemented in MPI. The canonical operation is perhaps
adding up a double precision number across all MPI processes, which is
best achieved by a *reduction operation*:

    MPI_Allreduce(&x, &xsum, 1, MPI_DOUBLE, MPI_SUM, MPI_COMM_WORLD);

This will be implemented using an efficient algorithm, for example based
on a binary tree. Using such *divide-and-conquer* approaches typically
results in an algorithm whose execution time on *P* processes scales
as *log_2(P)*; compare this to a naive approach where every process
sends its input to rank 0 where the time will scale as *P*. This might
not be significant on your laptop, but even on as few as 1000 processes
the tree-based algorithm will already be around 100 times faster.

So, the basic advice is **always use a collective routine to implement
your communications pattern** if at all possible.

In real MPI applications, collective operations are often called on a
small amount of data, for example a global reduction of a single
variable. In these cases, the time taken will be dominated by message
latency and the first port of call when looking at performance
optimisation is to call them as infrequently as possible\!

  - If you are simply printing diagnostics to the screen in an iterative
    loop, consider doing this less frequently, e.g every ten iterations,
    or even not at all (although you should easily be able to turn
    diagnostics on again for future debugging).
  - If you are computing some termination criterion, it may actually be
    faster overall to compute it and check for convergence infrequently,
    e.g. every ten iterations, even although this means that your
    program could run for up to 9 extra iterations.
  - If possible, group data into a single buffer and call a single
    reduction with count \> 1; two reductions with count = 1 will take
    almost exactly twice as long as a single reduction with count = 2.
  - For example, if you only need to output a sequence of summed data at
    the end of the run, store the partial totals in an array and do a
    single reduction right at the end.

Sometimes, the collective routines available may not appear to do
exactly what you want. However, they can sometimes be used with a small
amount of additional programming work:

  - To operate on a subset of processes, create sub-communicators
    containing the relevant subset(s) and use these communicators
    instead of `MPI_COMM_WORLD`. Useful functions for communicator
    management include:
    
      - `MPI_Comm_split` is the most general routine;
      - `MPI_Comm_split_type` can be used to create a separate
        communicator for each shared-memory node with `split type =
        MPI_COMM_TYPE_SHARED`;
      - `MPI_Cart_sub` can divide a Cartesian communicator into regular
        slices.

  - If the communication *pattern* is what you want, but the data on
    each process is not arranged in the required layout, consider using
    MPI derived data types for the input and/or output. This can be
    useful, for example, if you want to communicate non-contiguous data
    such as a subsection of a multidimensional array although care must
    be taken in defining these types to ensure they have the correct
    extents.
    
    Another example would be using `MPI_Allreduce` to add up an integer
    and a double-precision variable using a single call by putting them
    together into a C `struct` and defining a matching MPI datatype
    using `MPI_Type_create_struct`. Here you would also have to provide
    MPI with a custom reduction operation using `MPI_Op_create`.

Many MPI programs call `MPI_Barrier` to explicitly synchronise all the
processes. Although this can be useful for getting reliable performance
timings, it is rare in practice to find a program where the call is
actually needed for correctness. For example, you may see:

    // Ensure the input x is available on all processes
    MPI_Barrier(MPI_COMM_WORLD);
    // Perform a global reduction operation
    MPI_Allreduce(&x, &xsum, 1, MPI_DOUBLE, MPI_SUM, MPI_COMM_WORLD);
    // Ensure the result xsum is available on all processes
    MPI_Barrier(MPI_COMM_WORLD);

**Neither of these barriers are needed** as the reduction operation
performs all the required synchronisation.

If removing a barrier from your MPI code makes it run incorrectly, then
this should ring alarm bells -- it is often a symptom of an underlying
bug that is simply being masked by the barrier.

For example, if you use non-blocking calls such as `MPI_Irecv` then it
is the programmer's responsibility to ensure that these are completed at
some later point, for example by calling `MPI_Wait` on the returned
request object. A common bug is to forget to do this, in which case you
might be reading the contents of the receive buffer before the incoming
message has arrived (e.g. if the sender is running late).

Calling a barrier may mask this bug as it will make all the processes
wait for each other, perhaps allowing the late sender to catch up.
However, this is not guaranteed so the real solution is to call the
non-blocking communications correctly.

One of the few times when a barrier may be required is if processes are
communicating with each other via some other non-MPI method, e.g. via
the file system. If you want processes to sequentially open, append to,
then close the same file then barriers are a simple way to achieve this:

    for (i=0; i < size; i++)
    {
      if (rank == i) append_data_to_file(data, filename);
      MPI_Barrier(comm);
    }

but this is really something of a special case.

Global synchronisation may be required if you are using more advanced
techniques such as hybrid MPI/OpenMP or single-sided MPI communication
with put and get, but typically you should be using specialised routines
such as `MPI_Win_fence` rather than `MPI_Barrier`.

!!! tip
    If you run a performance profiler on your code and it shows a lot of
    time being spent in a collective operation such as `MPI_Allreduce`, this
    is *not necessarily* a sign that the reduction operation itself is the
    bottleneck. This is often a symptom of *load imbalance*: even if a
    reduction operation is efficiently implemented, it may take a long time
    to complete if the MPI processes do not all call it at the same time.
    `MPI_Allreduce` synchronises across processes so will have to wait for
    all the processes to call it before it can complete. A single slow
    process will therefore adversely impact the performance of your entire
    parallel program.

## OpenMP

There are a variety of possible issues that can result in poor performance of OpenMP programs. These include:

### Sequential code

Code outside of parallel regions is executed sequentially by the master thread.

### Idle threads

If different threads have different amounts of computation to do, then
threads may be idle whenever a barrier is encountered, for example at
the end of parallel regions or the end of worksharing loops. For
worksharing loops, choosing a suitable schedule kind may help. For
more irregular computation patterns, using OpenMP tasks might offer a
solution: the runtime will try to load balance tasks across the
threads in the team.

Synchronisation mechanisms that enforce mutual exclusion, such as
critical regions, atomic statements and locks can also result in idle
threads if there is contention - threads have to wait their turn for
access.

### Synchronisation

The act of synchronising threads comes at some cost, even if the
threads are never idle. In OpenMP, the most common source of
synchronisation overheads is the implicit barriers at the end of
parallel regions and worksharing loops. The overhead of these barriers
depends on the OpenMP implementation being used as well as on the
number of threads, but is typically in the range of a few
microseconds. This means that for a simple parallel loop such as

    #pragma omp parallel for reduction(+:sum)
    for (i=0;i<n;i++){
       sum += a[i];
    } 

the number of iterations required to make parallel execution
worthwhile may be of the order of 100,000. On ARCHER2, benchmarking
has shown that for the AOCC compiler, OpenMP barriers have
significantly higher overhead than for either the Cray or GNU
compilers.

It is possible to suppress the implicit barrier at the end of
worksharing loop using a `nowait` clause, taking care that this does
not introduce and race conditions.

Atomic statements are designed to be capable of more efficient
implementation that the equivalent critical region or lock/unlock pair,
so should be used where applicable.

### Scheduling

Whenever we rely on the OpenMP runtime to dynamically assign
computation to threads (e.g. dynamic or guided loop schedules, tasks),
there is some overhead incurred (some of this cost may actually be
internal synchronisation in the runtime).  It is often necessary to
adjust the granularity of the computation to find a compromise between
too many small units (and high scheduling cost) and too few large
units (where load imbalance may dominate). For example, we can choose
a non-default chunksize for the dynamic schedule, or adjust the amount of
computation within each OpenMP task construct.

### Communication

Communication between threads in OpenMP takes place via the cache
coherency mechanism. In brief, whenever a thread writes a memory
location, all copies of this location which are in a cache belonging
to a different core have to be marked as invalid. Subsequent accesses
to this location by other threads will result in the up-to-date value
being retrieved from the cache where the last write occurred (or
possibly from main memory).

Due to the fine granularity of memory accesses, these overheads are
difficult to analyse or monitor. To minimise communication, we need to
write code with good data affinity - i.e. each thread should access
the same subset of program data as much as possible.


#### NUMA effects

On modern CPU nodes, main memory is often organised in NUMA regions -
sections of main memory associated with a subset of the cores on a
node. On ARCHER2 nodes, there are 8 NUMA regions per node, each
associated with 16 CPU cores. On such systems the location of data in
main memory with respect to the cores that are accessing it can be
important. The default OS policy is to place data in the NUMA region
which first accesses it (first touch policy).  For OpenMP programs
this can be the worst possible option: if the data is initialised by
the master thread, it is all allocated one NUMA region and having all
threads accessing data becomes a bandwidth bottleneck.

This default policy can be changed using the `numactl` command, but it
is probably better to make use of the first touch policy by explicitly
parallelising the data initialisation in the application code. This
may be straightforward for large multidimensional arrays, but more
challenging for irregular data structures.

#### False sharing

The cache coherency mechanism described above operates on units of
data corresponding to the size of cache lines - for ARCHER2 CPUs this
is 64 bytes. This means that if different threads are accessing
neighbouring words in memory, and at least some of the accesses are
writes, then communication may be happening even if no individual word
is actually being accessed by more than one thread. This means that
patterns such as

   
    #pragma omp parallel shared(count) private(myid) 
    {
      myid = omp_get_thread_num();
      ....
      count[myid]++;
      ....
    }

may give poor performance if the updates to the `count` array are
sufficiently frequent.

### Hardware resource contention

Whenever there are multiple threads (or processes) executing inside a
node, they may contend for some hardware resources. The most important
of these for many HPC applications is memory bandwidth. This is effect
is very evident on ARCHER2 CPUs - it is possible for just 2 threads to
almost saturate the available memory bandwidth in a NUMA region which
has 16 cores associated with it. For very bandwidth-intensive
applications, running more that 2 threads per NUMA region may gain
little additional performance. If an OpenMP code is not using all the
cores on a node, by default Slurm will spread the threads out across
NUMA regions to maximise the available bandwidth.

Another resource that threads may contend for is space in shared
caches. On ARCHER2, every set of 4 cores shares 16MB of L3 cache. 


### Compiler non-optimisation

In rare cases, adding OpenMP directives can adversely affect the
compiler's optimisation process. The symptom of this is that the
OpenMP code running on 1 thread is slower than the same code compiled
without the OpenMP flag. It can be difficult to find a workaround -
using the compiler's diagnostic flags to find out which optimisation
(e.g. vectorisation, loop unrolling) is being affected and adding
compiler-specific directives may help.


## Hybrid MPI and OpenMP

There are two main motivations for using both MPI and OpenMP in the
same application code: reducing memory requirements and improving
performance. At low core counts, where the pure MPI version of the
code is still scaling well, adding OpenMP is unlikely to improve
performance. In fact, it can introduce some additional overheads which
make performance worse! The benefit is likely to come in the regime
where the pure MPI version starts to lose scalability - here adding
OpenMP can reduce communication costs, make load balancing easier, or
be an effective way of exploiting additional parallelism without
excessive code re-writing.

An important performance consideration for MPI + OpenMP applications
is the choice of the number of OpenMP threads per MPI process. The
optimum value will depend on the application, the input data, the
number of nodes requested and the choice of compiler, and is hard to
predict without experimentation. However, there are some
considerations that apply to ARCHER2:

* Due to NUMA effects, it is likely that running at least one MPI
  process per NUMA region (i.e. at least 8 MPI processes per node) will 
  be beneficial.

* The number of MPI processes per node should be a power of 2, so that
  all OpenMP threads run in the same NUMA region as their parent
  MPI process.

* For applications where each process has a small memory footprint
  (e.g. some molecular dynamics codes), running no more than 4 OpenMP
  threads per MPI process may be beneficial, so that all the threads
  in a process share a single L3 cache.





