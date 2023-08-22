# Energy use

This section covers how to monitor energy use for your jobs on ARCHER2 and how to control the CPU frequency
which allows some control over how much energy is consumed by jobs.

!!! important
    The default CPU frequency on ARCHER2 compute nodes for jobs launched using `srun` is currently set
    to 2.0 GHz. Information below describes how to control the CPU frequency using Slurm.

## Monitoring energy use

The Slurm accounting database stores the total energy consumed by a job and you can also directly access
the counters on compute nodes which capture instantaneous power and energy data broken down by different
hardware components.

### Using sacct to get energy usage for individual jobs

Energy usage for a particular job may be obtained using the `sacct` command. For instance

```
sacct -j 2658300 --format=JobID,Elapsed,ReqCPUFreq,ConsumedEnergy
```

will provide the elapsed time and consumed energy in joules for the job(s) specified with `-j`.
The output of this command is: 

```
JobID           Elapsed ReqCPUFreq ConsumedEnergy 
------------ ---------- ---------- -------------- 
2658300        02:19:48    Unknown          4.58M 
2658300.bat+   02:19:48          0          4.58M 
2658300.ext+   02:19:48          0          4.58M 
2658300.0      02:19:09    Unknown          4.57M 
```

In this case we can see that the job consumed 4.58 MJ for a run lasting 2 hours, 19 minutes and 48 seconds with
the CPU frequency unset. To convert the energy to kWh we can multiply the energy in joules by 2.78e-7, in this
case resulting in 1.27 kWh. 

The Slurm database may be cleaned without notice so you should gather any data you want as soon as possible after
the job completes - you can even add the `sacct` command to the end of your job script to ensure this data is
captured.

In addition to energy statistics `sacct` provides a number of other statistics that can be specified to the `--format`
option, the full list of which can be viewed with

```
sacct --helpformat
```

or using the `man` pages. 

### Accessing the node energy/power counters

!!! note
    The counters are available on each compute node and record data only for that compute node. If you are
    running multi-node jobs, you will need to combine data from multiple nodes to get data for the whole
    job. 

On compute nodes, the raw energy counters and instantaneous power draw data are available at:

```
/sys/cray/pm_counters
```

There are a number of files in this directory, all the counter files include the current value and a timestamp.

- power - Point-in-time power (Watts).
- energy - Accumulated energy (Joules)
- cpu_power - Point-in-time power (Watts) used by the CPU domain.
- cpu_energy - The total energy (Joules) used by the CPU domain.
- cpu*_temp - Temperature reading (Celsius) of the CPU domain - one file per CPU socket.
- memory_power - Point-in-time power (Watts) used by the memory domain.
- memory_energy - The total energy (Joules) used by the memory domain.
- generation - A counter that increments each time a power cap value is changed.
- startup - Startup counter.
- freshness - Free-running counter that increments at a rate of approximately 10Hz.
- version - Version number for power management counter support.
- power_cap - Current power cap limit in Watts; 0 indicates no capping.
- raw_scan_hz - The power management scanning rate for all data in pm_counters.

!!! note
    There exists an MPI-based wrapper library that can gather the `pm` counter values at runtime via a simple
    set of function calls. See the link below for details.

    - [Power Management MPI Library](../data-tools/pm-mpi-lib.md)

## Controlling CPU frequency

You can request specific CPU frequencies (in kHz) for compute nodes through `srun` options or environment variables.
The available frequencies on the ARCHER2 processors along with the options and environment variables:

| Frequency | `srun` option | Slurm environment variable | Turbo boost enabled? |
|----------:|--------------|----------------------------|-------|
| 2.25 GHz  | `--cpu-freq=2250000` | `export SLURM_CPU_FREQ_REQ=2250000` | Yes |
| 2.00 GHz  | `--cpu-freq=2000000` | `export SLURM_CPU_FREQ_REQ=2000000` | No |
| 1.50 GHz  | `--cpu-freq=1500000` | `export SLURM_CPU_FREQ_REQ=1500000` | No |

The only frequencies available on the processors on ARCHER2 are 1.5 GHz, 2.0 GHz and 2.25GHz.

For example, you can add the following option to `srun` commands in your job submission scripts to set the CPU frequency
to 2.25 GHz (and also enable turbo boost):

```
srun --cpu-freq=2250000 ...usual srun options and arguments...
```

Alternatively, you could add the following line to your job submission script before you use `srun`
to launch the application:

```
export SLURM_CPU_FREQ_REQ=2250000
```

!!! tip
    Testing by the ARCHER2 CSE team has shown that most software are most energy efficient when 2.0 GHz 
    is selected as the CPU frequency.

!!! tip
    When the highest frequency (2.25 GHz) is selected this also enables frequency turbo boost. Experiments
    on ARCHER2 have shown that under typical use, with all 128 cores heavily loaded, the processors
    turbo boost up to around 2.8 GHz when the frequency is set to 2.25 GHz.
    
    
!!! important
    The CPU frequency settings only affect applications launched using the `srun` command.

Priority of frequency settings:

- The default `SLURM_CPU_FREQ_REQ` setting set by the ARCHER2 service applies if no other mechnism 
  is used to set the CPU frequency
- Setting the `SLURM_CPU_FREQ_REQ` environment variable in a job script overrides options provided
  the default environment variable setting for any subsequent `srun` commands in the job script.
- Adding the `--cpu-freq=<freq in kHz>` option to the `srun` launch command itself overrides all other
  options.
  
!!! tip
    Adding the `--cpu-freq=<freq in kHz>` option to `sbatch` (e.g. using `#SBATCH --cpu-freq=<freq in kHz>`
    will not change the CPU frequency of `srun` commands used in the job as the default setting for ARCHER2
    will override the `sbatch` option when the script runs.

### Default CPU frequency

If you do not specify a CPU frequency then you will get the default setting for the ARCHER2 service
when you lanch an application using `srun`.
The table below lists the history of default CPU frequency settings on the ARCHER2 service

| Date range | Default CPU frequency |
|------------|-----------------------|
| 12 Dec 2022 - current date | 2.0 GHz | 
| Nov 2021 - 11 Dec 2022 | Unspecified - defaults to 2.25 GHz |  

### Slurm CPU frequency settings for centrally-installed software

Most [centrally installed research software](../research-software/) (available via `module load`
commands) uses the same default Slurm CPU frequency as set globally for all ARCHER2 users (see above
for this value). However, a small number of software have performance that is significantly 
degraded by using lower frequency settings and so the modules for these packages reset the 
CPU frequency to the highest value (2.25 GHz). The packages that currently do this are:

- [GROMACS](../research-software/gromacs.md)
- [LAMMPS](../research-software/lammps.md)
- [NAMD](../research-software/namd.md)

!!! important
    If you specify the Slurm CPU frequency in your job scripts using one of the mechanisms described
    above *after* you have loaded the module, you will override the setting from the module.



