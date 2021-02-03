# ARCHER2 Frequently Asked Questions

This section documents some of the questions raised to the Service Desk on ARCHER2, and the advice and solutions.

## User accounts

### Username already in use
**Q.** I created a machine account on ARCHER2 for a training course, but now I want to use that machine username for my main ARCHER2 project, and the system will not let me, saying "that name is already in use".  How can I re-use that username.

**A.**  Send an email to the [service desk](mailto:support@archer2.ac.uk), letting us know the username and project that you set up previously, and asking for that account and any associated data to be deleted.  Once deleted, you can then re-use that username to request an account in your main ARCHER2 project.

## Data

### ARCHER /home Data

**Q.** What is happening to data on `/home` on ARCHER and how can I access it from ARCHER2?

**A.** Our systems team have completed the final copy of the ARCHER `/home` filesystem and this is now available on ARCHER2.

If your home directory on ARCHER was: <br>&nbsp;&nbsp;&nbsp;&nbsp;
      `/home/[project]/[group]/[username]`

Then this data should be available on ARCHER2 at:<br>&nbsp;&nbsp;&nbsp;&nbsp;
      `/home/archer-home-backup/[project]/[group]/[username]`

This data is read-only and cannot be edited.

You should be able to read the data provided you requested the same username and the same project when creating your ARCHER2 account.

If this is not the case and you still need some of this data please contact the Service Desk, [support@archer2.ac.uk](mailto:support@archer2.ac.uk).

This backup will not be retained indefinitely so please copy any files you need to your ARCHER2 home directory as soon as you can.

### ARCHER /work Data

**Q.** What is happening to data on `/work` on ARCHER?

**A.** The hardware is being dismantled. All users are responsible for the transfer of any ARCHER `/work` data that they wish to keep.

There will be **no** access to `/work` after 0800 on Wednesday 27th January.


### RDF Data
**Q.** What is happening to data on the RDF (`/epsrc` and `/general`) and how can I access data on the RDF from ARCHER2?

**A.** Data on the RDF will persist beyond the lifetime of ARCHER. Although there are plans to make the RDF data directly available on ARCHER2 in the same way as they were on ARCHER, this functionality is not available yet. For the moment, you can transfer data from the RDF to ARCHER2 using scp/rsync as you would for any other remote host. You should use the host `login.rdf.ac.uk` to access the RDF data and use your ARCHER login credentials (you need to use both an SSH key and password as you do for ARCHER and ARCHER2). More information on transferring data to ARCHER2 using scp or rsync can be found [in the ARCHER2 User and Best Practice Guide](https://docs.archer2.ac.uk/user-guide/data/).

## Running on ARCHER2

### OOM error on ARCHER2

**Q.** Why is my code, which worked fine on ARCHER, failing on ARCHER2 with an 
out of memory (OOM) error?

**A.** While each ARCHER2 node has more memory than an ARCHER node, the 
large number of processor on ARCHER2 nodes means that there is slightly less 
memory per processor. This can result in jobs that ran fine on ARCHER failing 
because they use up all of the node memory. We recommend that you try running 
the same job on underpopulated nodes. This can be done by editing reducing the 
``--tasks-per-node`` in your Slurm submission script. Please lower it to half 
of its value when it fails (so if you have ``--tasks-per-node=128``, reduce it 
to ``--tasks-per-node=64``).

If the problem persists on underpopulated node, this may be a result of a 
known issue with the default version of MPICH. You can find more information 
on this issue (including a temporary workaround) in the 
[Known Issues](https://docs.archer2.ac.uk/known-issues/) section. 

### qstat, qsub 'Command not found'

**Q.** Commands such as `qstat` and `qsub` don't work - I get a "command not found" message - what am I doing wrong?

**A.** `qstat` and `qsub` were commands to access the PBS queue management system on ARCHER.

ARCHER2 uses Slurm instead of PBS - you can do all the same kinds of things but the commands are different.

The [Running jobs documentation](https://docs.archer2.ac.uk/user-guide/scheduler/) includes an
[introduction to Slurm commands](https://docs.archer2.ac.uk/user-guide/scheduler/#basic-slurm-commands). 

