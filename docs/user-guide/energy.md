# Energy use

This section covers how to monitor energy use for your jobs on ARCHER2 and how to control the CPU frequency
which allows some control over how much energy is consumed by jobs.

!!! important
    The default CPU frequency on ARCHER2 compute nodes is currently unset. This means that the cores will
    run at the maximum frequency available - 2.25 GHz. We anticipate that the default CPU frequency will
    be set to 2.0 GHz in early December 2022 to improve the energy efficiency of the ARCHER2 system.

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
- energy - Accumulated energy, in joules.
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

## Controlling CPU frequency

You can request specific CPU frequencies for compute nodes through Slurm options or environment variables.
The available frequencies on the ARCHER2 processors along with the options and environment variables:

| Frequency | Slurm option | Slurm environment variable |
|----------:|--------------|----------------------------|
| 2.25 GHz  | `--cpu-freq=2250000` | `export SLURM_CPU_FREQ_REQ=2250000` |
| 2.00 GHz  | `--cpu-freq=2000000` | `export SLURM_CPU_FREQ_REQ=2000000` |
| 1.50 GHz  | `--cpu-freq=1500000` | `export SLURM_CPU_FREQ_REQ=1500000` |

For example, you can add the following option to your job submission script to set the CPU frequency
to 2.0 GHz:

```
#SBATCH --cpu-freq=2000000
```

Alternatively, you could add the following line to your job submission script before you use `srun`
to launch the application:

```
export SLURM_CPU_FREQ_REQ=2000000
```

!!! tip
    Testing by the ARCHER2 CSE team has shown that many software are most energy efficient when 2.0 GHz 
    is selected as the CPU frequency. Typically, users may see savings of 10-20% of the energy cost of
    a job for a reduction in performance of 1-5%.

Priority of frequency settings:

- The default `SLURM_CPU_FREQ_REQ` setting set by the ARCHER2 service applies if no other mechnism 
  is used to set the CPU frequency
- Adding the `#SBATCH --cpu-freq=2000000` option to a job submission script overrides the default
  ARCHER2-wide environment setting for all `srun` commands within the job script.
- Setting the `SLURM_CPU_FREQ_REQ` environment variable in a job script overrides options provided
  to `sbatch` for any subsequent `srun` commands in the job script.
- Adding the `--cpu-freq=2000000` option to the `srun` launch command itself overrides all other
  options.

### Default CPU frequency

If you do not specify a CPU frequency then you will get the default setting for the ARCHER2 service.
The table below lists the history of default CPU frequency settings on the ARCHER2 service

| Date range | Default CPU frequency |
|------------|-----------------------|
| Nov 2021 - current date | Unspecified - defaults to 2.25 GHz |  

### CPU frequency settings for centrally-installed software

Most [centrally installed research software](../research-software/) (available via `module load`
commands) uses the same default CPU frequency as set globally for all ARCHER2 users (see above
for this value). However, a small number of software have performance that is significantly 
degraded by using lower frequency settings and so the modules for these packages reset the 
CPU frequency to the highest value (2.25 GHz). The packages that currently do this are:

- [GROMACS](../research-software/gromacs.md)
- [LAMMPS](../research-software/lammps.md)
- [NAMD](../research-software/namd.md)

!!! important
    If you specify the CPU frequency in your job scripts using one of the mechanisms described
    above *after* you have loaded the module, you will override the setting from the module.



