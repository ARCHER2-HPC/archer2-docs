# Energy use and emissions

This section covers energy use and greenhouse gas (GHG) emissions from ARCHER2.

The emissions section describes how to estimate emissions from your use of 
ARCHER2 and the methodology we have used to produce emissions estimates for the
service.

The energy section describes how to monitor energy use for your jobs on ARCHER2
and how to control the CPU frequency which allows some control over how much
energy is consumed by jobs.

!!! important
    The default CPU frequency cap on ARCHER2 compute nodes for jobs launched using `srun` is currently set
    to 2.0 GHz. Information below describes how to control the CPU frequency cap using Slurm.

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
- energy - Accumulated energy (Joules).
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

This documentation is from the official HPE documentation:

- [HPE Cray Operating System Administration Guide: CSM on HPE Cray EX
Systems](https://support.hpe.com/hpesc/public/docDisplay?docLocale=en_US&docId=dp00002748en_us)

!!! tip
    The overall `power` and `energy` counters include all on-node systems. The major components
    are the CPU (processor), memory and Slingshot network interface controller (NIC).

!!! note
    There exists an MPI-based wrapper library that can gather the `pm` counter values at runtime via a simple
    set of function calls. See the link below for details.

    - [Power Management MPI Library](../data-tools/pm-mpi-lib.md)

## Controlling CPU frequency

You can request specific CPU frequency caps (in kHz) for compute nodes through `srun` options or environment variables.
The available frequency caps on the ARCHER2 processors along with the options and environment variables:

| Frequency | `srun` option | Slurm environment variable | Turbo boost enabled? |
|----------:|--------------|----------------------------|-------|
| 2.25 GHz  | `--cpu-freq=2250000` | `export SLURM_CPU_FREQ_REQ=2250000` | Yes |
| 2.00 GHz  | `--cpu-freq=2000000` | `export SLURM_CPU_FREQ_REQ=2000000` | No |
| 1.50 GHz  | `--cpu-freq=1500000` | `export SLURM_CPU_FREQ_REQ=1500000` | No |

The only frequency caps available on the processors on ARCHER2 are 1.5 GHz, 2.0 GHz and 2.25GHz+turbo.

!!! important
    Setting the CPU frequency cap in this way sets the *maximum* frequency that the processors can use.
    In practice, the individual cores may select different frequencies up to the value you have set depending
    on the workload on the processor.

!!! important
    When you select the highest frequency value (2.25 GHz), you also enable turbo boost and so
    the processor is free to set the CPU frequency to values above 2.25 GHz if possible within
    the power and thermal limits of the processor. We see that, with turbo boost enabled,
    the processors typically boost to around 2.8 GHz even when performing compute-intensive work.

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

## Emissions

In this section we provide a brief overview of greenhouse gas (GHG) emissions sources relevant to ARCHER2, show how 
we have estimated the emissions associated with the service and describe how users can estimate
emissions associated with their use of ARCHER2.

### Emission sources

The emissions from ARCHER2 potentially fall into two categories (wording inspired by the Green Software Practitioner course linked below):

- Scope 2: Indirect emissions related to emission generation of purchased energy, such as heat and electricity. On ARCHER2 this would correspond to any emissions from the electricity used by the service. As we will see, the Scope 2 emissions are reported as zero because the energy contract for the service is based on 100% renewable energy.
- Scope 3: Other indirect emissions from the service. For ARCHER2, this corresponds to the embodied emissions of the hardware and infrastructure from the service, i.e. the emissions from manufacturing, shipping and decommissioning ARCHER2 hardware and supporting physical infrastructure.

The other class of emissions (Scope 1) are not relevant for the ARCHER2 service:

- Scope 1: Direct emissions from the service, such as on-site fuel combustion or fleet vehicles. There is nothing of this type currently associated with the ARCHER2 service.

If you want to learn more about GHG emissions in the area of software and digital infrastructure then you may want to 
look at the Green Software Foundation [Green Software Practitioner online course](https://learn.greensoftware.foundation/).

### ARCHER2 emissions

!!! important
    All ARCHER2 emissions are estimated and you should understand that there is the potential for
    significant variation from the current values as understanding of emissions values and 
    sources improves. 

#### Scope 3 emissions

Scope 3 emissions from the ARCHER2 hardware have been estimated from a subset of the components that are expected to 
make up the majority of the emissions. Note that there is a large amount of uncertainty for Scope 3 emissions due
to lack of high quality Scope 3 emissions data from vendors. In particular, the number used for the compute node
emissions is at the high end of estimated values and the actual value could be as much as 15% lower at around 
900 kgCO<sub>2</sub>e/node.

| Component | Count | Estimated kgCO<sub>2</sub>e per unit | Estimated kgCO<sub>2</sub>e | % Total Scope 3 | References |
|---|--:|--:|--:|--:|---|
| Compute nodes | 5,860 nodes | 1,100 | 6,400,000 | 84% | (1) |
| Interconnect switches | 768 switches | 280 | 150,000 | 2% | (2) |
| Lustre HDD | 19,759,200 GB | 0.02 | 400,000 | 6% | (3) |
| Lustre SSD | 1,900,800 GB | 0.16 | 300,000 | 4% | (3) |
| NFS HDD | 3,240,000 GB | 0.02 | 70,000 | 1% | (3) |
| Total | | | 7,320,000 | 100% | |

We then estimate the per-CU (nodeh) Scope 3 emissions by assuming a service lifetime of 6 years and
100% availability:

```
7,320,000 kgCO2e / (5,860 nodes * 6 years * 365 days * 24 hours) = 0.023 kgCO2e/CU
```

Tools use a value of **0.023 kgCO<sub>2</sub>e/CU** for ARCHER2.

References:

1. [IRISCAST Final Report](https://doi.org/10.5281/zenodo.7692451)
2. Estimate taken from IBM z16™ multi frame 24-port Ethernet Switch Product Carbon Footprint
3. [Tannu and Nair, 2023](https://arxiv.org/abs/2207.10793)

#### Scope 2 emissions

Scope 2 emissions from ARCHER2 are zero as the service is supplied by 100% certified renewable energy.
For information purposes we can calculate what the Scope 2 emissions would have been if the energy
was not 100% renewable energy using the methodology described below.

UK National Grid based Scope 2 emissions are calculated using the compute node energy use for particular
jobs along with the carbon intensity of the South Scotland region of the UK National Grid at the start
time of the job. The carbon intensity is retrieved from the [carbonintensity.org.uk](carbonintensity.org.uk)
web API.

If the energy use of a job is not available (which happens occasionally due to, e.g. counter failures) then
the mean per node power draw from 1 Jan 2024 - 30 Jun 2024 on ARCHER2 is used to compute the energy
consumption. This corresponds to a value of 0.41 kW per node.

Estimates of power draw of individual components of ARCHER2 suggest that the compute node power draw makes up
around 85% of the system power draw so using just the compute node power draw is a reasonable estimate.

| Component | Count | Loaded power draw per unit (kW)| Loaded power draw (kW) | % Total | Notes |
|---|--:|--:|--:|--:|---|
| Compute nodes | 5,860 nodes | 0.41 | 2,400 | 85% | Measured by on system counters |
| Interconnect switches | 768 switches | 0.24 | 240 | 9% | Measured by on system counters |
| Lustre storage | 5 file systems | 8 | 40 | 1% | Estimate from vendor |
| NFS storage | 4 file systems | 8 | 32 | 1% | Estimate from vendor |
| Coolant distribution units | 6 CDU | 16 | 96 | 3% | Estimate from vendor |
| Total | | | 2,808 | 99% | |

Current Scope 2 grid based emission calculations estimates do not include overheads from the electrical
and cooling plant, these will vary with outside weather conditions at the data centre but are typically
less than 10%.

We are aware that there is ongoing discussion in the sustainability community about the impact and
effectiveness of certified renewable energy contracts that are supplied through UK National Grid
connections. We are monitoring these discussions and taking advice from sustainability professionals
on how we report and estimate ARCHER2 emissions.

### Estimating your emissions

To help estimate GHG emissions from your use of ARCHER2 and place them in context to other sources of
GHG emissions we are developing a number of tools. We will add more information on these tools in 
this section of the documentation as they become available.

At the moment, the following tools are available:

- `jobemissions` - a command line tool on ARCHER2 that reports estimated emissions for a specified,
  completed job. It can also provide comparisons to other GHG emissions sources
- [ARCHER2 CU Calculator](https://www.archer2.ac.uk/support-access/cu-calc.html) provides estimated
  emissions for the specified CU use of ARCHER2

#### `jobemissions` tool

The `jobemissions` tool is available by default to all ARCHER2 users from the command line. You
supply a Slurm job ID for a completed job and the tool provides an estimate of the GHG emissions
associated with that job (based on the estimation methodologies described above). For example, to
provide an estimate for the completed job with Job ID 7654321, you would use:

```bash
jobemissions 7654321
```

Typical output from the tool would look like:

```
  Job details
       Job ID:                   7654321
        Start:       2024-11-11T20:50:00
       Budget:                       t01
        Nodes:                        20
      Runtime:                    324000 s
           CU:                  1800.000
   Energy use:                   448.973 kWh

  Emissions estimates:
      Scope 2:      0.000 kgCO2e (ARCHER2 is on 100% certified
               renewable energy contract so scope emissions are zero)
      Scope 3:     41.400 kgCO2e (23.0 gCO2e/CU)
        Total:     41.400 kgCO2e

      Indicative emissions estimates for UK national grid energy mix
      in S. Scotland at start of job if ARCHER2 was not using
      renewable energy
          Scope 2:      7.633 kgCO2e (448.973 kWh, 17.0 gCO2e/kWh)
          Scope 3:     41.400 kgCO2e (23.0 gCO2e/CU)
            Total:     49.033 kgCO2e

      Scope 2 carbon intensity values from carbonintensity.org.uk

```

If you add the flag `--comparison food,other` the tool will add comparisons of
GHG emissions for the job to other sources. e.g. for the same job above, it 
would add the following section to the end of the output.

```
   Emissions from job approximately equivalent to following food consumption:
        |      Food |  Emissions (kgCO2e/100g) | Equivalent to (g) |
        |-----------|--------------------------|-------------------|
        |      Beef |                    12.47 |            332.00 |
        |   Chicken |                     1.43 |           2895.10 |
        |   Avocado |                     0.18 |          23000.00 |
        | Chickpeas |                     0.04 |         103500.00 |

  Emissions from job approximately equivalent to:
        Daily emissions from 329.2 houses' electricity use (in S. Scotland)
        Emissions from flying 0.083 times across the Atlantic (500.00 kgCO2e/person)
        Emissions from driving 153.9 miles (0.27 kgCO2e/mile, average UK car, petrol and diesel very similar)

```

You can add the `--json` flag to obtain the emissions data from the tool in a machine-readable
format.

#### ARCHER2 CU Calculator

The [ARCHER2 CU Calculator](https://www.archer2.ac.uk/support-access/cu-calc.html) on the ARCHER2
website is used by potential users to estimate the number and cost of resources for potential
applications to use ARCHER2. This tool has been augmented to include an estimate of GHG emissions
from the proposed use of ARCHER2. In this tool, we include the Scope 3 emissions calculated 
as per the methodology above and note that Scope 2 emissions are zero due to the 100% renewable 
energy contract used to power ARCHER2.



