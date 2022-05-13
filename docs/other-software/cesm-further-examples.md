# Further Examples CESM 2.1.3

This page contains four example cases which have been validated with longer runs. They vary in the numbers of cores or threads used, but if you use the PE layout as described here, it should work. If you want to change details, or mix the details of one case in another, feel free to try.

The commands are all really similar for each case.

## Atmosphere / F2000climo

F09 grid, which should give around 7.8 simulated years per (wallclock) day (SYPD) if you use four nodes.

```bash
${CIMEROOT}/scripts/create_newcase --case [case name] --compset F2000climo --res f09_f09_mg17 --walltime [enough time] --project [project code]
./xmlchange NTASKS=512,NTASKS_ESP=1
cd [case directory]
[Any other changes e.g. run length or resubmissions]
./case.setup
./case.build
./case.submit
```

## Slab Ocean / ETEST

This is similar to the above, as the slab ocean is inexpensive. Two OMP threads are used, and more tasks, so you should get just under 20 SYPD. Unlike F2000climo, but like most compsets, this is unsupported so requires an extra argument when creating the case.

```bash
${CIMEROOT}/scripts/create_newcase --case [case name] --compset F2000climo --res f09_f09_mg17 --walltime [enough time] --project [project code] --run-unsupported
cd [case directory]
./xmlchange NTASKS=1024,NTASKS_ESP=1
./xmlchange NTHRDS=2
[Any other changes e.g. run length or resubmissions]
./case.setup
./case.build
./case.submit
```

## Coupled Ocean / B1850

This case has a more expensive ocean component. You can set this up to run on dedicated nodes by changing the ROOTPE (run the ./pelayout command to check that you have things as you wish). This should give just over 10 SYPD.

```bash
${CIMEROOT}/scripts/create_newcase --case [case name] --compset B1850 --res f09_g17 --walltime [enough time] --project [project name]
cd [case directory]
./xmlchange NTASKS_CPL=1024,NTASKS_ICE=256,NTASKS_LND=256,NTASKS_GLC=128,NTASKS_ROF=128,NTASKS_WAV=256,NTASKS_OCN=512,NTASKS_ATM=1024
./xmlchange ROOTPE_CPL=0,ROOTPE_ICE=0,ROOTPE_LND=256,ROOTPE_GLC=512,ROOTPE_ROF=640,ROOTPE_WAV=768,ROOTPE_OCN=1024,ROOTPE_ATM=0
./case.setup
./case.build
./case.submit
```

## WACCM-X / FXHIST

This case needs care during the set up and running, as it can pick up other files in the cesm_inputdata directory, or move too many files in the archiving (the archiving is turned off below). It should give over 1.5 SYPD on a coarser grid than above, as is much more expensive.

```bash
${CIMEROOT}/scripts/create_newcase --case [case name] --compset FXHIST --res f19_f19_mg16 --walltime [enough time] --project [project name] --run-unsupported
cd [case directory]
./xmlchange NTASKS=1024,NTASKS_ESP=1
./xmlchange NTHRDS=2
./case.setup
./case.build
./case.submit
```

