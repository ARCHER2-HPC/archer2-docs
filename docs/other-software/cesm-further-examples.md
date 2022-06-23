# Further Examples CESM 2.1.3

In the process of porting CESM 2.1.3 to ARCHER2, a set of 4 long runs were carried out. This page contains the four example cases which have been validated with longer runs. They vary in the numbers of cores or threads used, but included here are the PE layouts used in these validation runs, which can be used as a guide for other runs. While only these four compsets and grids have been validated, CESM2 is not bound to just these cases. Links to the UCAR/NCAR pages on configurations, compsets and grids are in the [useful links](cesm213.md#useful-links) section of the CESM2.1.3 on ARCHER2 page, which can be used to find many of the defined compsets for CESM2.1.3.

## Atmosphere-only / F2000climo

This compset uses the F09 grid which is roughly equivalent to a 1 degree resolution. On ARCHER2 with four nodes this configuration should give a throughput of around 7.8 simulated years per (wallclock) day (SYPD). The commands to set up and run the case are as follows:

```bash
${CIMEROOT}/scripts/create_newcase --case [case name] --compset F2000climo --res f09_f09_mg17 --walltime [enough time] --project [project code]
cd [case directory]
./xmlchange NTASKS=512,NTASKS_ESP=1
[Any other changes e.g. run length or resubmissions]
./case.setup
./case.build
./case.submit
```

## Slab Ocean / ETEST

The slab ocean case is similar to the atmosphere-only case in terms of resources needed, as the slab ocean is inexpensive to simulate in comparison to the atmosphere. The setup detailed below uses two OMP threads, and more tasks than were used by the F2000climo case, and so a throughput of around 20 SYPD can be expected. Unlike F2000climo, but like most compsets, this is unsupported (meaning it has not been scientifically verified by NCAR personnel) and as such an extra argument is required when creating the case. The arguments for ROOTPE are to guard against poor decisions being automatically chosen with respect to resources.

```bash
${CIMEROOT}/scripts/create_newcase --case [case name] --compset ETEST --res f09_g17 --walltime [enough time] --project [project code] --run-unsupported
cd [case directory]
./xmlchange NTASKS=1024,NTASKS_ESP=1
./xmlchange NTHRDS=2
./xmlchange ROOTPE_ICE=0,ROOTPE_OCN=0
[Any other changes e.g. run length or resubmissions]
./case.setup
./case.build
./case.submit
```

## Coupled Ocean / B1850

Compsets with the `B` prefix are fully coupled, and actively simulate all components. As such, This case is more expensive to run, most especially the ocean component. This case can be set up to run on dedicated nodes by changing the `$ROOTPE` variables (run the ./pelayout command to check that you have things as you wish). This should give a throughput of just over 10 SYPD.

```bash
${CIMEROOT}/scripts/create_newcase --case [case name] --compset B1850 --res f09_g17 --walltime [enough time] --project [project name]
cd [case directory]
./xmlchange NTASKS_CPL=1024,NTASKS_ICE=256,NTASKS_LND=256,NTASKS_GLC=128,NTASKS_ROF=128,NTASKS_WAV=256,NTASKS_OCN=512,NTASKS_ATM=1024
./xmlchange ROOTPE_CPL=0,ROOTPE_ICE=0,ROOTPE_LND=256,ROOTPE_GLC=512,ROOTPE_ROF=640,ROOTPE_WAV=768,ROOTPE_OCN=1024,ROOTPE_ATM=0
[Any other changes e.g. run length or resubmissions]
./case.setup
./case.build
./case.submit
```

You can also define the PE layout in terms of full nodes by using negative values. As such, for a `$MAX_MPITASKS_PER_NODE=128` and `$MAX_TASKS_PER_NODE=128`, the below is equivalent to the above:

```bash
${CIMEROOT}/scripts/create_newcase --case [case name] --compset B1850 --res f09_g17 --walltime [enough time] --project [project name]
cd [case directory]
./xmlchange NTASKS_CPL=-8,NTASKS_ICE=-2,NTASKS_LND=-2,NTASKS_GLC=-1,NTASKS_ROF=-1,NTASKS_WAV=-2,NTASKS_OCN=-4,NTASKS_ATM=-8
./xmlchange ROOTPE_CPL=0,ROOTPE_ICE=0,ROOTPE_LND=-2,ROOTPE_GLC=-4,ROOTPE_ROF=-5,ROOTPE_WAV=-6,ROOTPE_OCN=-8,ROOTPE_ATM=0
[Any other changes e.g. run length or resubmissions]
./case.setup
./case.build
./case.submit
```

## WACCM-X / FXHIST

The WACCM-X case needs care during the set up and running for a couple of reasons. Firstly, as mentioned in the [known issues section on archiving errors](cesm213_run.md#archiving-errors) the short-term archiver can sometimes move too many files and thus create problems with resubmissions. Secondly, it can pick up other files in the cesm_inputdata directory, causing issues when running. WACCM-X is also comparatively very expensive, and so only has an expected throughput of a little over 1.5 SYPD, and that when on a coarser grid than above. The setup for running a WACCM-X case with approximately 2 degree resolution and no short-term archiving is

```bash
${CIMEROOT}/scripts/create_newcase --case [case name] --compset FXHIST --res f19_f19_mg16 --walltime [enough time] --project [project name] --run-unsupported
cd [case directory]
./xmlchange NTASKS=512,NTASKS_ESP=1
./xmlchange NTHRDS=2
./xmlchange DOUT_S=FALSE
[Any other changes e.g. run length or resubmissions]
./case.setup
./case.build
./case.submit
```
