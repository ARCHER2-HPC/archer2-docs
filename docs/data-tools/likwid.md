# LIKWID

[LIKWID](https://github.com/RRZE-HPC/likwid) is an open-source tool
suite that can be used to measure node-level hardware performance
counters, amongst other functionalities. It offers ways to quantify
performance, e.g. when investigating performance bottlenecks or
degradation. In this documentation we present guidance for common use
cases on ARCHER2, focusing on performance counter measurement for
parallel applications. For more information on LIKWID functionality
and usage see [the official LIKWID
wiki](https://github.com/RRZE-HPC/likwid/wiki).

## likwid-perfctr and likwid-mpirun

LIKWID provides a number of command line tools. Performance counter
measurement is handled by
[`likwid-perfctr`](https://github.com/RRZE-HPC/likwid/wiki/likwid-perfctr),
which supports the following usage modes:

- [**wrapper
  mode**](https://github.com/RRZE-HPC/likwid/wiki/likwid-perfctr#basic-usage-wrapper-mode)
  (default): use `likwid-perfctr` as a wrapper to launch your
  application and measure performance counters while it executes. This
  is the easiest way to ensure that measurement starts and stops when
  your application starts and stops and that the cores whose counters
  are included in measurements are those on which your application
  executes.

    + [**wrapper mode + marker
      API**](https://github.com/RRZE-HPC/likwid/wiki/likwid-perfctr#using-the-marker-api):
      when using wrapper mode it is possible to measure performance
      counters only during execution of one or more specific regions
      in your code, for example to quantify the performance of known
      computationally costly kernels. This requires instrumenting your
      code to use the [LIKWID marker
      API](https://github.com/RRZE-HPC/likwid/wiki/LikwidAPI-and-MarkerAPI)
      and recompiling.


- [**stethoscope
  mode**](https://github.com/RRZE-HPC/likwid/wiki/likwid-perfctr#the-stethoscope-mode):
  launch your application as usual, then instruct `likwid-perfctr` to
  measure performance counters associated with the cores on which your
  application is executing. Measurements are aggregated over a
  duration of time that you specify. It may be difficult to relate
  results to what application code was executed over the measurement
  duration. This mode is more suited to obtaining a snapshot of
  performance than to performing a systematic performance assessment.


- [**timeline
  mode**](https://github.com/RRZE-HPC/likwid/wiki/likwid-perfctr#the-timeline-mode):
  launch your application as usual, then instruct `likwid-perfctr` to
  periodically output performance counter measurements aggregated over
  the time interval specified. As for stethoscope mode you must tell
  `likwid-perfctr` which cores to measure counters for, ensuring these
  match where your application is executing. This mode can provide
  insight into performance during different phases of your
  application, though it may be difficult to relate results to what
  application code was executed over any given measurement interval.

Using `likwid-perfctr` in any of the above modes other than wrapper +
marker API can be done without altering or recompiling your
application code.

`likwid-perfctr` is designed to work with serial or thread-parallel
applications. Measuring counters for MPI-parallel applications may be
accomplished in principle by combining `likwid-perfctr` and a parallel
application launcher such as `srun` as described [in this
tutorial](https://github.com/RRZE-HPC/likwid/wiki/TutorialMPI).
LIKWID provides a more elegant solution in the form of a wrapper
called
[`likwid-mpirun`](https://github.com/RRZE-HPC/likwid/wiki/Likwid-Mpirun).
This launches `likwid-perfctr` and your MPI-parallel application and
aggregates measurement outputs across ranks.

!!! note

    `likwid-mpirun` only supports `likwid-perfctr`'s wrapper mode
    (with or without marker API)


## LIKWID on ARCHER2

For the sake of simplicity and convenience we provide guidance on how
to use `likwid-mpirun` to measure performance counters on ARCHER2
regardless of whether the application is serial, thread-parallel,
MPI-parallel, or hybrid MPI + thread-parallel. This unified approach
has the added benefit of being closer to the ARCHER2 default
`srun`-based application launch approach used in existing job scripts
than using `likwid-perfctr`.

Using `likwid-mpirun` restricts measurement functionality to
`likwid-perfctr`'s wrapper mode, which supports performance
characterisation through either whole-application or kernel-specific
measurement. Users interested in running `likwid-perfctr` directly on
ARCHER2, for example to access timeline or stethoscope mode, may
consult the example job script for pure threaded applications that
uses `likwid-perfctr` below as well as the [wiki page on LIKWID's
pinning syntax](https://github.com/RRZE-HPC/likwid/wiki/Likwid-Pin),
and can contact the ARCHER2 helpdesk to request assistance.

LIKWID is available on ARCHER2 as a centrally installed module
(`likwid/5.4.1-archer2`), which provides a [customised
version](https://github.com/EPCCed/likwid/tree/archer2) of the
official 5.4.1 release. The primary difference compared to the
official release is that `likwid-mpirun` has been adapted for improved
compatibility with ARCHER2 Slurm configuration, default job launch
recommendations and the Cray MPI library. It offers the `--nocpubind`
option, which we introduced and recommend using to leave binding of
application processes to CPUs to `srun` to control as per the standard
approach for running jobs on ARCHER2. The central LIKWID install also
incorporates [several more minor
changes](https://github.com/RRZE-HPC/likwid/compare/master...EPCCed:likwid:archer2). The
guidance and example job scripts presented below pertain to this
custom version and its usage on ARCHER2.


!!! note

    LIKWID on ARCHER2 uses the [perf-event
    backend](https://github.com/RRZE-HPC/likwid/wiki/TutorialLikwidPerf)
    with `perf_event_paranoid` set to -1 (no restrictions), which has
    [some implications for
    features/functionality](https://github.com/RRZE-HPC/likwid/wiki/TutorialLikwidPerf#feature-limitations)












## Summary of likwid-mpirun options

The following options are important to be aware of when using
`likwid-mpirun` on ARCHER2. For additional information, try
`likwid-mpirun --help` and see the LIKWID wiki, especially the
[`likwid-mpirun`
page](https://github.com/RRZE-HPC/likwid/wiki/Likwid-Mpirun).


`-n/-np <count>`

Specify the total number of processes to launch


`-t <count>`

The number of threads or threads per MPI rank. Defaults to 1 if not
specified. Can be used to "space" processes for placement in the case
of hybrid MPI + threaded applications and act as an alternative to
`-pin` below in these cases. 

`-pin <list>`

Specify pinning of processes (and their threads if
applicable). Follows the
[`likwid-pin`](https://github.com/RRZE-HPC/likwid/wiki/Likwid-Pin)
syntax. 

`-g/--group <perf>`

Specify which predefined group of performance counters and derived
metrics to measure and compute. Details about these groups and
available counters for the Zen2 architecture of ARCHER2's AMD EPYC
processors can be found at
[https://github.com/RRZE-HPC/likwid/wiki/Zen2](https://github.com/RRZE-HPC/likwid/wiki/Zen2).


`--nocpubind` (ARCHER2 only)

Suppress `likwid-mpirun`'s binding of application processes to CPUs
(cores) that would otherwise take place through generation of a CPU
mask list passed to `srun`. We recommend always using `--nocupbind` on
ARCHER2 to avoid conflicts with binding/affinity specified following
the usual approach on ARCHER2 and this is shown in example job
scripts.


`-s/--skip <hex>`

This tells `likwid-mpirun` how many threads to skip when generating
its specification of which cores to pin to/measure (see [this
discussion of LIKWID and shepherd
threads](https://blogs.fau.de/hager/archives/8171) for a detailed
explanation). On ARCHER2 we have checked and confirmed that there are
no shepherd threads involved using any of `PrgEnv-gnu`, `PrgEnv-cray`
or `PrgEnv-aocc`, therefore not skipping any threads (`-s 0x0`) is the
correct choice (this differs from `likwid-mpirun` defaults), reflected
in the example job scripts above.

`-d/--debug`

To check how exactly `likwid-mpirun` calls `srun` and `likwid-perfctr`
to launch and measure your application, use the `--debug` option,
which will generate additional output that includes the relevant
commands. For the modified `likwid-mpirun` on ARCHER2 the otherwise
temporary files `.likwidscript_*.txt` referenced in debug output that
contain these commands persist after execution, enabling closer
inspection.


`--mpiopts`

Any desired options accepted by `srun` can be passed to the `srun`
command through the `--mpiopts` option, for example as follows:

`likwid-mpirun --mpiopts "--exact --hint=nomultithread --distribution=block:block"` 

`-m/-marker`

Activate Marker API mode
















## Example job scripts

Below we provide example job scripts covering different cases of
application parallelism using `likwid-perfctr` in wrapper mode either
through `likwid-mpirun` or directly. All examples perform
whole-application measurement but can be adapted to use the marker
API.

Each of the example job scripts that uses `likwid-mpirun` makes use of
the srun/sbatch options `--hint=nomultithread` and
`--distribution=block:block`. We have set these as SBATCH options at
the top of the job script. Alternatively these as well as any other
`srun` options could be passed explicitly to the underlying `srun`
command through the `--mpiopts` option as follows:

`likwid-mpirun --mpiopts "--hint=nomultithread --distribution=block:block"`  

Each example job script includes a suggested command to run `xthi`
launched identically to the application you wish to measure in order
to check and confirm that process and thread placement for your
application is as intended. Details on checking process placement with
`xthi` can be found in the User Guide page on [Running
jobs](https://docs.archer2.ac.uk/user-guide/scheduler/).

!!! note

    You are encouraged to check how `likwid-mpirun` uses `srun` to
    launch `likwid-perfctr` and your application by running in debug
    mode (`likwid-mpirun --debug`) and examining both the job output
    and the `.likwidscript-####` file mentioned therein.

For pure threaded and MPI+thread parallel jobs using either the `-t`
option to specify the number of threads or `-pin` with an appropriate
pinning expression (or both) can accomplish the same desired
application placement and measurement scenario. The same applies to
pure MPI applications in the case of underpopulating nodes (i.e. fewer
than 128 ranks per node), where `-t` can be used to space processes
out across a node and/or `-pin` used to specify which cores on each
node application processes should execute and be measured on.

For further explanation of `likwid-mpirun` options used, see the
section [Summary of likwid-mpirun
options](#summary-of-likwid-mpirun-options)



### Pure MPI jobs

#### Fully populated node(s)

Two fully populated nodes (128 ranks per node):

```slurm
#!/bin/bash

#SBATCH --account=[your project]
#SBATCH --partition=standard
#SBATCH --qos=short
#SBATCH --time=00:20:00 
#SBATCH --nodes=2
#SBATCH --tasks-per-node=128
#SBATCH --cpus-per-task=1
#SBATCH --hint=nomultithread
#SBATCH --distribution=block:block

module load likwid
module load xthi

export OMP_NUM_THREADS=1
export SRUN_CPUS_PER_TASK=1

likwid-mpirun -n $SLURM_NTASKS --nocpubind -s 0x0 -g FLOPS_DP --debug xthi_mpi &> xthi_mpi.out
likwid-mpirun -n $SLURM_NTASKS --nocpubind -s 0x0 -g FLOPS_DP myApplication &> application.out

```


#### Underpopulated node(s)


Two nodes, two ranks per node, one rank per socket (i.e. per 64-core AMD EPYC processor):

```slurm
#!/bin/bash

#SBATCH --account=[your project]
#SBATCH --partition=standard
#SBATCH --qos=short
#SBATCH --time=00:20:00 
#SBATCH --nodes=2
#SBATCH --tasks-per-node=2
#SBATCH --cpus-per-task=64
#SBATCH --hint=nomultithread
#SBATCH --distribution=block:block

module load likwid
module load xthi

export OMP_NUM_THREADS=1
export SRUN_CPUS_PER_TASK=$SLURM_CPUS_PER_TASK

likwid-mpirun -n $SLURM_NTASKS -pin N:0_N:64 --nocpubind -s 0x0 -g FLOPS_DP --debug xthi_mpi &> xthi_mpi.out
likwid-mpirun -n $SLURM_NTASKS -pin N:0_N:64 --nocpubind -s 0x0 -g FLOPS_DP myApplication &> application.out

```

The same application placement and measurement scenario can be
accomplished by specifying the first core on each socket directly with
`-pin S0:0_S1:0` instead of `-pin N:0_N:64`


One node, four ranks, one rank per NUMA region all on the same socket:

```slurm
#!/bin/bash

#SBATCH --account=[your project]
#SBATCH --partition=standard
#SBATCH --qos=short
#SBATCH --time=00:20:00 
#SBATCH --nodes=1
#SBATCH --tasks-per-node=4
#SBATCH --cpus-per-task=16
#SBATCH --hint=nomultithread
#SBATCH --distribution=block:block

module load likwid
module load xthi

export OMP_NUM_THREADS=1
export SRUN_CPUS_PER_TASK=16

likwid-mpirun -n $SLURM_NTASKS -pin N:0_N:16_N:32_N:48 --nocpubind -s 0x0 -g FLOPS_DP --debug xthi_mpi &> xthi_mpi.out
likwid-mpirun -n $SLURM_NTASKS -pin N:0_N:16_N:32_N:48 --nocpubind -s 0x0 -g FLOPS_DP myApplication &> application.out

```

The same application placement and measurement scenario can be
accomplished by specifying the first core on each NUMA node directly
with `-pin M0:0_M1:0_M2:0_M3:0` instead of `-pin N:0_N:16_N:32_N:48`






### Pure threaded jobs

#### Fully populated node

```slurm
#!/bin/bash

#SBATCH --account=[your project]
#SBATCH --partition=standard
#SBATCH --qos=short
#SBATCH --time=00:20:00 
#SBATCH --nodes=1
#SBATCH --tasks-per-node=1
#SBATCH --cpus-per-task=128
#SBATCH --hint=nomultithread
#SBATCH --distribution=block:block

module load likwid
module load xthi

export OMP_NUM_THREADS=128
export OMP_PLACES=cores
export SRUN_CPUS_PER_TASK=128

likwid-mpirun -n 1 -t 128 --nocpubind -s 0x0 -g FLOPS_DP --debug xthi &> xthi.out
likwid-mpirun -n 1 -t 128 --nocpubind -s 0x0 -g FLOPS_DP myApplication &> application.out

```

The same application placement and measurement scenario can be
accomplished using the pinning option `-pin N:0-127` instead of `-t
128`.

For pure threaded applications the `likwid-perfctr` command can also
be used directly instead of `likwid-mpirun`, bypassing `srun`. This is
shown below in the equivalent job script to the fully populated
`likwid-mpirun` example above.


```slurm
#!/bin/bash

#SBATCH --account=[your project]
#SBATCH --partition=standard
#SBATCH --qos=short
#SBATCH --time=00:20:00 
#SBATCH --nodes=1

module load likwid
module load xthi

export OMP_NUM_THREADS=128
export OMP_PLACES=cores

likwid-perfctr -C N:0-127 -s 0x0 -g FLOPS_DP --debug xthi &> xthi.out
likwid-perfctr -C N:0-127 -s 0x0 -g FLOPS_DP myApplication &> application.out

```

The `-C` option simultaneously sets pinning of application threads to cores and
specifies those same cores to measure counters for. The following pinning
expressions are equivalent:

```
-C N:0-127
-C E:N:128:1:2
```

The second form uses LIKWID's expression based pinning syntax ([see
the `likwid-pin` wiki
page](https://github.com/RRZE-HPC/likwid/wiki/Likwid-Pin)). This can
be understood by examining CPU numbering using the `likwid-topology`
command, which shows that adjacent hardware threads on the same
physical cores are numbered n and n+128 respectively, hence CPUs
(logical cores) numbered 0 through 127 correspond to single hardware
threads on each of the 128 distinct physical cores on an ARCHER2
compute node. The final `:2` in the expression based syntax skips the
second hardware thread for each physical core when pinning, thereby
accomplishing the same as the domain-based pinning expression that
specifies direct core number assignment for the N domain (all cores).


#### Underpopulated node

Launching fewer than 128 threads placed consecutively is a
straightforward variation on the fully occupied node case above. We
can achieve more varied placements, for example four threads in total,
each bound to the first core of a different CCX complex on the same
socket. The 4 cores in a CCX share a common L3 cache, hence this
scenario results in none of the threads sharing the same L3 cache.

This is easiest to accomplish using `likwid-perfctr` directly rather
than through `likwid-mpirun`, as follows:

```slurm
#!/bin/bash

#SBATCH --account=[your project]
#SBATCH --partition=standard
#SBATCH --qos=short
#SBATCH --time=00:20:00 
#SBATCH --nodes=1

module load likwid
module load xthi

export OMP_NUM_THREADS=4
export OMP_PLACES=cores

likwid-perfctr -C 0,4,8,12 -s 0x0 -g FLOPS_DP xthi &> xthi_perfctr_list.out

```

The same application placement and measurement scenario can be
accomplished using the pinning option `-C C0:0@C1:0@C2:0@C3:0`
instead. This specifies threads be assigned to the first core of
successive last cache level (L3) domains. The expression syntax
version `-C E:N:4:1:8` would achieve the same.




### Hybrid MPI+threaded jobs

2 fully populated nodes with 2 ranks per node and 64 threads per rank:

```slurm
#!/bin/bash

#SBATCH --account=[your project]
#SBATCH --partition=standard
#SBATCH --qos=short
#SBATCH --time=00:20:00 
#SBATCH --nodes=2
#SBATCH --tasks-per-node=2
#SBATCH --cpus-per-task=64
#SBATCH --hint=nomultithread
#SBATCH --distribution=block:block

module load likwid
module load xthi

export OMP_NUM_THREADS=64
export OMP_PLACES=cores
export SRUN_CPUS_PER_TASK=64

likwid-mpirun -n $SLURM_NTASKS -t 64 --nocpubind -s 0x0 -g FLOPS_DP --debug xthi &> xthi.out
likwid-mpirun -n $SLURM_NTASKS -t 64 --nocpubind -s 0x0 -g FLOPS_DP myApplication &> application.out

```

The same application placement and measurement scenario can be
accomplished using the pinning option `-pin N:0-63_N:64-127` instead
of `-t 64`.


### Serial job

Using `likwid-mpirun`:

```slurm
#!/bin/bash

#SBATCH --account=[your project]
#SBATCH --partition=standard
#SBATCH --qos=short
#SBATCH --time=00:20:00 
#SBATCH --nodes=1
#SBATCH --tasks-per-node=1
#SBATCH --cpus-per-task=1

module load likwid
module load xthi

export OMP_NUM_THREADS=1
export SRUN_CPUS_PER_TASK=1

likwid-mpirun -n 1 --nocpubind -s 0x0 -g FLOPS_DP --debug xthi &> xthi.out
likwid-mpirun -n 1 --nocpubind -s 0x0 -g FLOPS_DP myApplication &> application.out

```

Alternatively, using `likwid-perfctr`:

```slurm
#!/bin/bash

#SBATCH --account=[your project]
#SBATCH --partition=standard
#SBATCH --qos=short
#SBATCH --time=00:20:00 
#SBATCH --nodes=1

module load likwid
module load xthi

export OMP_NUM_THREADS=1

likwid-perfctr -C 0 --nocpubind -s 0x0 -g FLOPS_DP --debug xthi &> xthi.out
likwid-perfctr -C 0 --nocpubind -s 0x0 -g FLOPS_DP myApplication &> application.out

```



