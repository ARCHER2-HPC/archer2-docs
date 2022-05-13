# Quick Start: CESM Model Workflow (CESM 2.1.3)
---

This is the procedure for quickly setting up and running a simple CESM2 case on ARCHER2. This document is based on the general [quickstart guide for CESM 2.1](https://escomp.github.io/CESM/versions/cesm2.1/html/quickstart.html), with modifications to give instructions specific to ARCHER2. For more expansive instructions on running CESM 2.1, please consult the [NCAR CESM pages](https://escomp.github.io/CESM/versions/cesm2.1/html/introduction.html)

Before following these instructions, ensure you have completed the setup procedure (see [Setting up CESM2 on ARCHER2](cesm213_setup.md)).

For your target case, the first step is to select a component set, and a resolution for your case. For the purposes of this guide, we will be looking at a simple coupled case using the `B1850` compset and the `f19_g17` resolution.

The current configuration of CESM 2.1.3 on ARCHER2 has been validated with the F2000 (atmosphere only), ETEST (slab ocean), B1850 (fully coupled) and FX2000 (WACCM-X) compsets. Instructions for these are here: [CESM2.1.3 further examples](cesm-further-examples.md).

[comment]: # (Give a link to white paper here discussing testing and validation runs etc. once prepared)

Details of available component sets and resolutions are available from the [query_config](http://esmci.github.io/cime/versions/master/html/users_guide/introduction-and-overview.html#discovering-available-cases-with-query-config) tool located in the `my_cesm_sandbox/cime/scripts` directory

``` {.console}
cd my_cesm_sandbox/cime/scripts
./query_config --help
```

See the [supported component
sets](http://www.cesm.ucar.edu/models/cesm2/config/compsets.html), [supported model
resolutions](http://www.cesm.ucar.edu/models/cesm2/config/grids.html) and [supported machines](http://www.cesm.ucar.edu/models/cesm2/config/machines.html) for a complete list of CESM2 supported component sets, grids and computational platforms.

> **_Note_**:
Variables presented as `$VAR` in this guide typically refer to variables in XML files in a CESM case. From within a case directory, you can determine the value of such a variable with `./xmlquery VAR`. In some instances, `$VAR` refers to a shell variable or some other variable; we try to make these exceptions clear.

## Preparing a case

There are three stages to preparing the case: create, setup and build. Here you can find information on each of these steps

### 1. Create a case

The [create_newcase](http://esmci.github.io/cime/versions/master/html/users_guide/create-a-case.html) command creates a case directory containing the scripts and XML files to configure a case (see below) for the requested resolution, component set, and machine. **create_newcase** has three required arguments: `--case`, `--compset` and `--res` (invoke **create_newcase \--help** for help).

On machines where a project or account code is needed (including ARCHER2), you must either specify the `--project` argument to **create_newcase** or set the `$PROJECT` variable in your shell environment.

If running on a supported machine, that machine will normally be recognized automatically and therefore it is *not* required to specify the `--machine` argument to **create_newcase**. For CESM 2.1.3, ARCHER2 is classed as an *unsupported* machine, however the configurations for ARCHER2 are included in the version of cime downloaded in the setup process, and so adding the `--machine` flag should not be necessary.

Invoke **create_newcase** as follows:

``` {.console}
./create_newcase --case CASENAME --compset COMPSET --res GRID --project PROJECT
```

where:

-   `CASENAME` defines the name of your case (stored in the `$CASE` XML     variable). This is a very important piece of metadata that will be     used in filenames, internal metadata and directory paths.     **create_newcase** will create the *case directory* with the same     name as the `CASENAME`. If `CASENAME` is simply a name (not a path),     the case directory is created in the directory where you executed     create_newcase. If `CASENAME` is a relative or absolute path, the     case directory is created there, and the name of the case will be     the last component of the path. The full path to the case directory     will be stored in the `$CASEROOT` XML variable. See [CESM2     Experiment     Casenames](http://www.cesm.ucar.edu/models/cesm2/naming_conventions.html#casenames)     for details regarding CESM experiment case naming conventions.
-   `COMPSET` is the [component set](http://www.cesm.ucar.edu/models/cesm2/config/compsets.html).
-   `GRID` is the model
    [resolution](http://www.cesm.ucar.edu/models/cesm2/config/grids.html).
-   `PROJECT` is you project code on ARCHER2.

Here is an example on ARCHER2 with the CESM2 module loaded:

``` {.console}
$CIMEROOT/scripts/create_newcase --case $CESM_ROOT/runs/b.e20.B1850.f19_g17.test --compset B1850 --res f19_g17 --project n02
```

### 2. Setting up the case run script

Issuing the [case.setup](http://esmci.github.io/cime/versions/master/html/users_guide/setting-up-a-case.html) command creates scripts needed to run the model along with namelist `user_nl_xxx` files, where xxx denotes the set of components for the given case configuration. Before invoking **case.setup**, modify the `env_mach_pes.xml` file in the case directory using the [xmlchange](http://esmci.github.io/cime/versions/master/html/Tools_user/xmlchange.html) command as needed for the experiment.

cd to the case directory. Following the example from above:

``` {.console}
cd $CESM_ROOT/runs/b.e20.B1850.f19_g17.test
```

Invoke the **case.setup** command.

``` {.console}
./case.setup
```

If any changes are made to the case, **case.setup** can be re-run using

``` {.console}
./case.setup --reset
```

### 3. Build the executable using the case.build command

Run the build script.

``` {.console}
./case.build
```

This build may take a while to run, and have periods where the build process doesn't seem to be doing anything. You should only cancel the build if there has been no activity by the build script after 15 minutes.

The CESM executable will appear in the directory given by the XML variable `$EXEROOT`, which can be queried using:

``` {.console}
./xmlquery EXEROOT
```

by default, this will be the `bld` directory in your case directory.

If any changes are made to xml parameters that would necessitate rebuilding (see the [Making Changes](#making-changes-to-a-case) section below), then you can apply these by running

``` {.console}
./case.setup --reset
./case.build --clean-all
./case.build
```
## Input Data

Each case of CESM will require input data, which is downloaded from UCAR servers. Input data from similar compsets is often reused, so running two similar cases may not require downloading any additional input data for the second case.

You can check to see if the required input data is already in your input data directory using

``` {.console}
./check_input_data
```

If it is not present you can download the input data for the case prior to running the case using

``` {.console}
./check_input_data --download
```

This can be useful for cases where a large amount of data is needed, as you can write a simple slurm script to run this download on the serial queue. Information on creating job submission scripts can be found on the [ARCHER2 page on Running Jobs](https://docs.archer2.ac.uk/user-guide/scheduler/).

Downloading the case input data at this stage is optional, and if skipped the data will be downloaded using the login node when you run the **case.submit** script. This may cause the **case.submit** script to take a long time to download.

An important thing to note is that your input data will be stored in your /work area, and will contribute to your storage allocation. These input files can sometimes take up a large amount of space, and so it is recommended that you do not keep any input data that is no longer needed.

## Making changes to a case

After creating a new case, the CIME functions can be used to make changes to the case setup, such as changing the wallclock time, number of cores etc.

You can query settings using the **[xmlquery](https://esmci.github.io/cime/versions/master/html/Tools_user/xmlquery.html)** script from your case directory:

``` {.console}
./xmlquery <name_of_setting>
```

Adding the `-p` flag allows you to look up partial names, for example

``` {.console}
$ ./xmlquery -p JOB

Output:
Results in group case.run
        JOB_QUEUE: standard
        JOB_WALLCLOCK_TIME: 01:30:00

Results in group case.st_archive
        JOB_QUEUE: short
        JOB_WALLCLOCK_TIME: 0:20:00

```

Here all parameters that match the `JOB` pattern are returned. It is worth noting that the parameters `JOB_QUEUE` and `JOB_WALLCLOCK_TIME` are present for both the **case.run** job and the **case.st_archive** job. To view just one of these, you can use the `--subgroup` flag:

``` {.console}
$ ./xmlquery -p JOB --subgroup case.run

Output:
Results in group case.run
        JOB_QUEUE: standard
        JOB_WALLCLOCK_TIME: 01:30:00
```

When you know which setting you want to change, you can do so using the **[xmlchange](http://esmci.github.io/cime/versions/master/html/Tools_user/xmlchange.html)** command

``` {.console}
./xmlchange <name_of_setting>=<new_value>
```

For example to change the wallclock time for the **case.run** job to 30 minutes, without knowing the exact name, you could do

``` {.console}

$ ./xmlquery -p WALLCLOCK

Output:
Results in group case.run
        JOB_WALLCLOCK_TIME: 24:00:00

Results in group case.st_archive
        JOB_WALLCLOCK_TIME: 0:20:00

$ ./xmlchange JOB_WALLCLOCK_TIME=00:30:00 --subgroup case.run

$ ./xmlquery JOB_WALLCLOCK_TIME

Output:
Results in group case.run
        JOB_WALLCLOCK_TIME: 00:30:00

Results in group case.st_archive
        JOB_WALLCLOCK_TIME: 0:20:00
```


>***Note:*** If you try to set a parameter equal to a value that is not known to the program, it might suggest using a ``--force`` flag. This may be useful, for example, in the case of using a queue that has not been configured yet, but use with care!


Some changes to the case must be done before calling ``./case.setup`` or ``./case.build``, otherwise the case will need to be reset or cleaned, using ``./case.setup --reset`` and ``./case.build --clean-all``. These are as follows:

* Before calling ``./case.setup``, changes to ``NTASKS``, ``NTHRDS``, ``ROOTPE``, ``PSTRID`` and ``NINST`` must be made, as well as any changes to the ``env_mach_specific.xml`` file, which contains some configuration for the module environment and environment variables.

* Before calling ``./case.build``, ``./case.setup`` must have been called and any changes to ``env_build.xml`` and ``Macros.make`` must have been made. This includes whether you have edited the file directly, or used ``./xmlchange`` to alter the variables.

Many of the namelist variables can be changed just before calling ``./case.submit``.

## Run the case

Modify runtime settings in `env_run.xml` (optional). At this point you may want to change the running parameters of your case, such as run length. By default, the model is set to run for 5 days based on the `$STOP_N` and `$STOP_OPTION` variables:

``` {.console}
./xmlquery STOP_OPTION,STOP_N
```

These default settings can be useful in [troubleshooting](http://esmci.github.io/cime/versions/master/html/users_guide/troubleshooting.html) runtime problems before submitting for a longer time, but will not allow the model to run long enough to produce monthly history climatology files. In order to produce history files, increase the run length to a month or longer:

``` {.console}
./xmlchange STOP_OPTION=nmonths,STOP_N=1
```

Once you have set your job to run for the correct length of time, it is a good idea to check the correct amount of resource is available for the job. You can quickly check the job submission parameters by running

``` {.console}
./preview_run
```

which will show you at a glance the wallclock times, job queues and the list of jobs to be submitted, as well as other parameters such as the number of MPI tasks, number of OpenMP threads.


Submit the job to the batch queue using the **case.submit** command.

``` {.console}
./case.submit
```

The **case.submit** script will submit a job called **.case.run**, and if `$DOUT_S` is set to `TRUE` it will also submit a short-term archiving job. By default, the queue these jobs are submitted to is the `standard` queue. For information on the resources available on each queue, see the [QOS guide](https://docs.archer2.ac.uk/user-guide/scheduler/#quality-of-service-qos).

>**_Note_**:
There is a small possibility that your job may initially fail with the error message `ERROR: Undefined env var 'CESM_ROOT'`.
This could have two causes:
1. You do not have the CESM2/2.1.3 module loaded. This module needs to be loaded when running the case as well as when building the case. Try running again after having run `module load CESM2/2.1.3`
2. This could also be due to a known issue with ARCHER2 where adding the SBATCH directive `export=ALL` to a slurm script will not work (see the [ARCHER2 known issues entry](https://docs.archer2.ac.uk/known-issues/#slurm-export-option-does-not-work-in-job-submission-script) on the subject). The ARCHER2 configuration included in the version of cime that was downloaded during setup should apply a work-around to this, and so you should not see this error in this case. It may still occur in some corner cases however. To avoid this, ensure that the environment from which you are submitting your case has the CESM2/2.1.3 module loaded and run the **case.submit** script with the following command
``` {.console}
./case.submit -a=--export=ALL
```

When the job is complete, most output will not necessarily be written under the case directory, but instead under some other directories. Review the following directories and files, whose locations can be found with **xmlquery** (note: **xmlquery** can be run with a list of comma separated names and no spaces):

``` {.console}
./xmlquery RUNDIR,CASE,CASEROOT,DOUT_S,DOUT_S_ROOT
```

-   `$RUNDIR`

    This directory is set in the `env_run.xml` file. This is the location where CESM2 was run. There should be log files there for every component (i.e. of the form cpl.log.yymmdd-hhmmss) if `$DOUT_S == FALSE`. Each component writes its own log file. Also see whether any restart or history files were written. To check that a run completed successfully, check the last several lines of the cpl.log file for the string \"SUCCESSFUL TERMINATION OF CPL7-cesm\".

-   `$DOUT_S_ROOT/$CASE`

    `$DOUT_S_ROOT` refers to the short term archive path location on local disk. This path is used by the case.st_archive script when `$DOUT_S = TRUE`. See [CESM Model Output File Locations](http://www.cesm.ucar.edu/models/cesm2/naming_conventions.html#modelOutputLocations) for details regarding the component model output filenames and locations.

    `$DOUT_S_ROOT/$CASE` is the short term archive directory for this case. If `$DOUT_S` is FALSE, then no archive directory should exist. If `$DOUT_S` is TRUE, then log, history, and restart files should have been copied into a directory tree here.

-   `$DOUT_S_ROOT/$CASE/logs`

    The log files should have been copied into this directory if the run completed successfully and the short-term archiver is turned on with `$DOUT_S = TRUE`. Otherwise, the log files are in the `$RUNDIR`.

-   `$CASEROOT`

    There could be standard out and/or standard error files output from the batch system.

-   `$CASEROOT/CaseDocs`

    The case namelist files are copied into this directory from the `$RUNDIR`.

-   `$CASEROOT/timing`

    There should be two timing files there that summarize the model performance.

## Monitoring Jobs

As CESM jobs are submitted to the ARCHER2 batch system, they can be monitored in the same way as other jobs, using the command

``` {.console}
squeue -u $USER
```

You can get more details about the batch scheduler by consulting the [ARCHER2 scheduling guide](https://docs.archer2.ac.uk/user-guide/scheduler/).

## Archiving

The CIME framework allows for short-term and long-term archiving of model output. This is particularly useful when the model is configured to output to a small storage space and large files may need to be moved during larger simulations. On ARCHER2, the model is configured to use short-term archiving, but not yet configured for long-term archiving.

Short-term archiving is on by default for compsets and can be toggled on and off using the **DOUT_S** parameter set to True or False using the **xmlchange** script:

``` {.console}
./xmlchange DOUT_S=FALSE
```

When `DOUT_S=TRUE`, calling **./case.submit** will automatically submit a “st_archive” job to the batch system that will be held in the queue until the main job is complete. This can be configured in the same way as the main job for a different queue, wallclock time, etc. One change that may be advisable to make would be to change the queue your st_archive job is submitted to, as archiving does not require a large amount of resources and the short and serial queues on ARCHER2 do not use your project allowance. This would be done using the **xmlchange** script almost the same as for the **case.run** job. Note that the main job and the archiving job share some parameter names such as `JOB_QUEUE`, and so a flag (--subgroup) specifying which you want to change should be used, as below:

``` {.console}
./xmlchange JOB_QUEUE=short --subgroup case.st_archive
```

If the `--subgroup` flag is not used, then the `JOB_QUEUE` value for both the **case.run** and **case.st_archive** jobs will be changed. You can verify that they are different by running

``` {.console}
./xmlquery JOB_QUEUE
```

which will show the value of this parameter for both jobs.

The archive is set up to move `.nc` files and logs from `$CESM_ROOT/runs/$CASE` to `$CESM_ROOT/archive/$CASE`. As such, your `/work` storage quota is being used whether archiving is switched on or off, and so it would be recommended that data you wish to retain be moved to another service such as a group workspace on JASMIN. See the [Data Management and Transfer](https://docs.archer2.ac.uk/user-guide/data/) guide for more information on archiving data from ARCHER2. If you want to archive your files directly to a different location than the default, this can be set using the `$DOUT_S_ROOT` parameter.


## Troubleshooting

If a run fails, the first place to check is the run submission output file, usually located at

``` {.console}
$CASEROOT/run.$CASE
```

so, for the example job run in this guide, the output file will be at

``` {.console}
$CESM_ROOT/runs/b.e20.B1850.f19_g17.test/run.b.e20.B1850.f19_g17.test
```

If any errors have occurred, the location of the relevant log in which you can examine this error will be printed towards the end of this output file. The log will usually be located at

``` {.console}
$CASEROOT/run/cesm.log.*
```

so in this case, the path would be

``` {.console}
$CESM_ROOT/runs/b.e20.B1850.f19_g17.test/run/cesm.log.*
```

### Known Issues

#### SIGSEGV errors

Sometimes an error will occur where a run is ended prematurely and gives an error of the form

``` {.console}
Program received signal SIGSEGV: Segmentation fault - invalid memory reference.
```

This can often be solved by increasing the amount of available memory per task, either by changing the maximum number of MPI tasks per node by using

``` {.console}
./xmlchange MAX_TASKS_PER_NODE=64
```

or by increasing the number of threads used by using

``` {.console}
./xmlchange NTHRDS=2
```

This will double the amount of memory available for each physical core
