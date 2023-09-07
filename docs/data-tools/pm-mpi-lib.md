# Power Management MPI Library

The ARCHER2 compute nodes each have a set of so-called Power Management (PM) counters.
These cover point-in-time power readings for the whole node, and for the CPU and memory domains.
The accumulated energy use is also recorded at the same level of detail. Further, there are
two temperature counters, one for each socket/processor on the node. The counters are read
ten times per second and the data written to a set of files stored within node memory (located
at `/sys/cray/pm_counters/`).

For convenience, we have developed an MPI-based wrapper, called `pm_mpi_lib` that facilitates
the reading of the PM counter files, see the link below.

[https://github.com/cresta-eu/pm_mpi_lib](https://github.com/cresta-eu/pm_mpi_lib)

The PM MPI Library makes it possible to monitor the Power Management counters during the execution
of an MPI code running across *multiple* compute nodes. The library is lightweight, containing just
three functions, and is intended to be straightforward to use. You simply decide which parts of
your code you wish to profile as regards energy usage and/or power consumption.

As your code executes, the PM counters will be read at various points by a single designated
monitor rank on each node assigned to the job. These readings are then written to a log file,
which, after the job completes, will contain one set of time-stamped readings per node for
every call to the `pm_mpi_record` function made from within your code. The readings can then
be aggregated according to preference.

Further information along with test harnesses and example scripts can be found by reading
the [PM MPI Library readme file](https://github.com/cresta-eu/pm_mpi_lib).
