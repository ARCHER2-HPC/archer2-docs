# ARCHER2 Frequently Asked Questions

This section documents some of the questions raised to the Service Desk on ARCHER2, and the advice and solutions.

## User accounts

### Username already in use
**Q.** I created a machine account on ARCHER2 for a training course, but now I want to use that machine username for my main ARCHER2 project, and the system will not let me, saying "that name is already in use".  How can I re-use that username.

**A.**  Send an email to the [service desk](mailto:support@archer2.ac.uk), letting us know the username and project that you set up previously, and asking for that account and any associated data to be deleted.  Once deleted, you can then re-use that username to request an account in your main ARCHER2 project.

## Data

### ARCHER /home Data

**Q.** What is happening to data on `/home` on ARCHER and how can I access it from ARCHER2?

**A.** Once the ARCHER Service has disabled user access, the systems team will then complete a final copy of the ARCHER `/home` data.

Once the final copy is complete, it will be made available on ARCHER2 and you will be able to access it. To access this data seamlessly from ARCHER2, you should have the same username that you had on ARCHER when the data was created. Further details on how to access this data will be provided once the data is available on ARCHER2.

The systems team have taken an initial copy of the data and are performing regular top-ups. We request that only necessary changes to the `/home` directory structure are made at this time as this will reduce the duration of the final copy of the data.

We hope to have the ARCHER `/home` data available on ARCHER2 within a few days but this will depend on changes made to `/home` before the final copy.

Please ensure that you have transferred any data that you immediately require on ARCHER2. More information is available in the [Data Migration guide](https://docs.archer2.ac.uk/archer-migration/data-migration/).

### ARCHER /work Data

**Q.** What is happening to data on `/work` on ARCHER?

**A.** The hardware is being dismantled. All users are responsible for the transfer of any ARCHER `/work` data that they wish to keep.

There will be **no** access to `/work` after 0800 on Wednesday 27th January.


### RDF Data
**Q.** What is happening to data on the RDF (`/epsrc` and `/general`) and how can I access data on the RDF from ARCHER2?

**A.** Data on the RDF will persist beyond the lifetime of ARCHER. Although there are plans to make the RDF data directly available on ARCHER2 in the same way as they were on ARCHER, this functionality is not available yet. For the moment, you can transfer data from the RDF to ARCHER2 using scp/rsync as you would for any other remote host. You should use the host `login.rdf.ac.uk` to access the RDF data and use your ARCHER login credentials (you need to use both an SSH key and password as you do for ARCHER and ARCHER2). More information on transferring data to ARCHER2 using scp or rsync can be found [in the ARCHER2 User and Best Practice Guide](https://docs.archer2.ac.uk/user-guide/data/).

## Running on ARCHER2

### OOM error on ARCHER2

**Q.** Why is my code, that worked fine on ARCHER, failing on ARCHER2 with an 
out of memory (OOM) error?

**A.** While each ARCHER2 node has more memory than the ARCHER nodes, the 
large number of processor on ARCHER2 means that there is slightly less memory 
per processor. We recommend that you try running the same job on 
underpopulated nodes. This can be done by editing reducing the 
``--tasks-per-node`` in your Slurm submission script. Please lower it to half 
of its value when it fails (so if you have ``--tasks-per-node=128``, reduce it 
to ``--tasks-per-node=64``).

If the problem persists on underpopulated node, it might be the resulte of a 
known issue with the default version of MPICH. You can find more information 
on this (including a temporary workaround) in the [Known Issues](https://docs.archer2.ac.uk/known-issues/) section. 
