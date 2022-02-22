# Energy monitoring on ARCHER2

## Using sacct to get energy usage for individual jobs

Energy usage for a particular job may be obtained using the `sacct` command. For instance

    sacct -j 00001 --format=JobID,Elapsed,ConsumedEnergy

will provide the elapsed time and consumed energy in joules for the job(s) specified with `-j`. In this case we would
get something like

           JobID    Elapsed ConsumedEnergy 
    ------------ ---------- -------------- 
    00001          00:01:50         59.53K 
    00001.bat+     00:01:50         59.16K 
    00001.ext+     00:01:50         59.53K 
    00001.0        00:01:44         58.80K 

In this case we can see that the job consumed 59.53kJ for a run lasting 1 minute and 50 seconds. To convert to kWh we
can multiply the energy in joules by 2.78e-7, in this case resulting in 0.0165kWh. 

Data for completed jobs is stored for up to 24 hours after the job completes, so usage statistics must be gathered soon
after the job finishes. 

In addition to energy statistics `sacct` provides a number of other statistics that can be specified to the `--format`
option, the full list of which can be viewed with

    sacct --helpformat

or using the `man` pages. 
