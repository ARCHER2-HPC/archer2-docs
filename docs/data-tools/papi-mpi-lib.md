# PAPI MPI Library

The [Performance Application Programming Interface (PAPI)](https://icl.utk.edu/papi/) is an API that facilitates the 
reading of performance counter data without needing to know the details of the underlying hardware. 

For convenience, we have developed an MPI-based wrapper for PAPI, called `papi_mpi_lib`, which can be found via the
link below.

[https://github.com/cresta-eu/papi_mpi_lib](https://github.com/cresta-eu/papi_mpi_lib)

The PAPI MPI Library makes it possible to monitor a user-defined set of hardware performance
counters during the execution of an MPI code running across *multiple* compute nodes. The
library is lightweight, containing just four functions, and is intended to be straightforward
to use. Once you've decided where in your code you wish to record counter values, you can
control which counters are read at runtime by setting the `PAT_RT_PERFCTR` environment
variable in the job submission script. As your code executes, the specified counters
will be read at various points. After each reading, the counter values are summed by rank 0
(via an MPI reduction) before being output to a log file.

You can discover which counters are available on ARCHER2 compute nodes by submitting the
following single node job.

```bash
#!/bin/bash --login
  
#SBATCH -J papi
#SBATCH --time=00:20:00
#SBATCH --exclusive
#SBATCH --nodes=1
#SBATCH --tasks-per-node=1
#SBATCH --cpus-per-task=1
#SBATCH --account=<budget code>
#SBATCH --partition=standard
#SBATCH --qos=short
#SBATCH --export=none

function papi_query() {
  export LD_LIBRARY_PATH=/opt/cray/pe/papi/$2/lib64:/opt/cray/libfabric/$3/lib64
  module -q restore

  module -q load cpe/$1
  module -q load papi/$2

  mkdir -p $1
  papi_component_avail -d &> $1/papi_component_avail.txt
  papi_native_avail -c &> $1/papi_native_avail.txt
  papi_avail -c -d &> $1/papi_avail.txt
}

papi_query 22.12 6.0.0.17 1.12.1.2.2.0.0
```

The job runs various `papi` commands with the output being directed to specific text files.
Please consult the text files to see which counters are available. Note, counters that
are not available may still be listed in the file, but with a label such as `<NA>`.

As of July 2023, the Cray Programming Environment (CPE), PAPI and libfabric versions
on ARCHER2, were `22.12`, `6.0.0.17` and `1.12.1.2.2.0.0` respectively; these versions may change in
the future.

Alternatively, you can run `pat_help counters rome` from a login node to check the availability of
individual counters.

Further information on `papi_mpi_lib` along with test harnesses and example scripts can be found by reading
the [PAPI MPI Library readme file](https://github.com/cresta-eu/papi_mpi_lib).
