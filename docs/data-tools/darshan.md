# Darshan

Darshan is a scalable HPC I/O characterization tool. Darshan is designed to capture
an accurate picture of application I/O behavior, including properties such as patterns
of access within files, with minimum overhead.  The name is taken from a Sanskrit
word for "sight" or "vision". 

Darshan is developed at the [Argonne Leadership Computing Facility (ALCF)](https://www.alcf.anl.gov/)

Useful links:

- [Darshan home page](https://www.mcs.anl.gov/research/projects/darshan/)
- [Darshan documentation](https://www.mcs.anl.gov/research/projects/darshan/documentation/)

## Using Darshan on ARCHER2

Using Darshan generally consists of two stages:

1. Collect IO profile data using the Darshan runtime
2. Analysing Darshan log files using Darshan utility software

### Collecting IO profile data

To collect IO profile data you add the command:


```
module load darshan
```

to your job submission script as the **last** `module` command before you run your program. As Darshan
does not distinguish between different software run in your job submission script, we typically 
recommand that you use a structure like:

```
module load darshan
srun ...usual software launch options...
module remove darshan
```

This will avoid Darshan profiling IO for operations that are not part of your main parallel program.

!!! important
    The `darshan` module is dependent on the compiler environment you are using and you should ensure
    that you load the `darshan` module that matches the compiler environment you used to compile the
    program you are analysing. For example, if your software was compiled using `PrgEnv-gnu`, then you
    would need to activate the GCC compiler environment before loading the `darshan` module to ensure you
    get the GCC version of Darshan. This means loading the correct `PrgEnv-` module before you load the
    `darshan` module:

    ```
    module load PrgEnv-gnu
    module load darshan
    srun ...usual software launch options...
    module remove darshan
    ```

### Location of Darshan profile logs

Darshan writes all profile logs to a shared location on the ARCHER2 NVMe Lustre file system. You can
find your profile logs at:

```
/mnt/lustre/a2fs-nvme/system/darshan/YYYY/MM/DD
```

where `YYYY/MM/DD` correspond to the date on which your job ran.

### Analysing Darshan profile logs

The simplest way to analyse the profile log files is to use the `darshan-parser` utility on the 
ARCHER2 login nodes. You make the Darshan analysis utilities available with the command:

```
module load darshan-util
```

Once this is loaded, you can produce and IO performance summary from a profile log file with:

```
darshan-parser --prof /path/to/darshan/log/file.darshan
```

You can get a dump of all data in the Darshan profile log by omitting the `--perf` option, e.g.:

```
darshan-parser /path/to/darshan/log/file.darshan
```

!!! tip
    The `darshan-job-summary.pl` and `darshan-summary-per-file.sh` utilities do not work on ARCHER2
    as the required graphical packages are not currently available.

Documentation on the Darshan analysis utilities are available at:

- [darshan-util documentation](https://www.mcs.anl.gov/research/projects/darshan/docs/darshan-util.html)
- [PyDarshan module - Python interface to analyses Darshan profile logs](https://www.mcs.anl.gov/research/projects/darshan/docs/pydarshan/index.html)

