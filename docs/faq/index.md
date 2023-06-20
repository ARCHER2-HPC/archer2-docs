# ARCHER2 Frequently Asked Questions

This section documents some of the questions raised to the Service Desk on ARCHER2, and the advice and solutions.

## User accounts

### Username already in use
**Q.** I created a machine account on ARCHER2 for a training course, but now I want to use that machine username for my main ARCHER2 project, and the system will not let me, saying "that name is already in use".  How can I re-use that username.

**A.**  Send an email to the [service desk](mailto:support@archer2.ac.uk), letting us know the username and project that you set up previously, and asking for that account and any associated data to be deleted.  Once deleted, you can then re-use that username to request an account in your main ARCHER2 project.

## Data


### Undeleteable file .nfsXXXXXXXXXXX

**Q.**  I have a file called .nfsXXXXXXXXXXX (where XXXXXXXXXXX is a long hexadecimal string) in my /home folder but I can't delete it.

**A.** This file will have been created during a file copy which failed.  Trying to delete it will give an error "Device or resource busy", even though the copy has ended and no active task is locking it.

`echo -n >.nfsXXXXXXXXXXX`

will remove it.

## Running on ARCHER2

### OOM error on ARCHER2

**Q.** Why is my code failing on ARCHER2 with an out of memory (OOM) error?

**A.** You are requesting too much memory per process. We recommend that you try running
the same job on underpopulated nodes. This can be done by editing reducing the
``--ntasks-per-node`` in your Slurm submission script. Please lower it to half
of its value when it fails (so if you have ``--ntasks-per-node=128``, reduce it
to ``--ntasks-per-node=64``).

### Checking budgets

**Q.**  How can I check which budget code(s) I can use?

**A.**  You can check in [SAFE](https://safe.epcc.ed.ac.uk) by selecting `Login accounts` from the menu, select the login account you want to query.

Under `Login account details` you will see each of the budget codes you have access to listed e.g.
`e123 resources` and then under Resource Pool to the right of this, a note of the remaining budget.

When logged in to the machine you can also use the command

    sacctmgr show assoc where user=$LOGNAME format=user,Account%12,MaxTRESMins,QOS%40

This will list all the budget codes that you have access to (but not the amount of budget available) e.g.

        User      Account  MaxTRESMins                                 QOS
    -------- ------------ ------------ -----------------------------------
       userx    e123-test                   largescale,long,short,standard
       userx         e123        cpu=0      largescale,long,short,standard

This shows that `userx` is a member of budgets `e123-test` and `e123`.  However, the `cpu=0` indicates that the `e123` budget is empty or disabled.   This user can submit jobs using the `e123-test` budget.

You can only check the amount of available budget via SAFE - [see above](#checking-budgets).


### Estimated start time of queued jobs

**Q.**  I’ve checked the estimated start time for my queued jobs using “squeue -u $USER --start”. Why does the estimated start time keep changing?

**A.**  ARCHER2 uses the Slurm scheduler to queue jobs for the compute nodes. Slurm attempts to find a better schedule as jobs complete and new jobs are added to the queue. This helps to maximise the use of resources by minimising the number of idle compute nodes, in turn reducing your wait time in the queue.  

However, If you periodically check the estimated start time of your queued jobs, you may notice that the estimate changes or even disappears. This is because Slurm only assigns the top entries in the queue with an estimated start time. As the schedule changes, your jobs could move in and out of this top region and thus gain or lose an estimated start time.
