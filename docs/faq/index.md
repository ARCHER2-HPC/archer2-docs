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

### Jobs failures

Your job has failed with an ugly message from the scheduler. What can be done?
Here are some common causes, and possible remedies.


#### Out-of-memory error OOM

**Q.** Why is my code failing on ARCHER2 with an out of memory (OOM) error?

**A.** If you see a message of the following form at the end of your job
output:
```
slurmstepd: error: Detected 1 oom-kill event(s) in StepId=7935598.0. \
Some of your processes may have been killed by the cgroup out-of-memory handler.
```
your job has requested too much memory on one or more nodes (the maximum
is 256 GB shared between all processes for nodes in the standard partition).
This may
typically happen shortly after the job has started (one or two minutes).
In this case, you need to provide more memory.

1. Try running the same job on the same number of MPI processes, but
use more nodes (and hence more memory).  This can be done by reducing
the `--ntasks-per-node` value in your Slurm submission script; e.g.,
if you have `--ntasks-per-node=128` you can try `--ntasks-per-node=64`
and double the number of nodes via `--nodes`.
2. If using standard partition nodes, one can also try running on the
same number of MPI processes, but use the `highmem` partition in
which the nodes have twice as much memory as the standard partition.
3. If there is still a problem, you may need to reduce the size of
your problem until you understand where the limit is.

More rarely, OOM errors may occur long after the job as started (many hours).
This is suggestive of a memory leak, and your code will need to be debugged.
If you are using a standard package, please contact the Service Desk with
enough information that the problem can be replicated. A poorly designed
application can also exhibit this problem if, for example, there is a
significant request for new memory late in execution (e..g, at a final
configuration output). This should be remedied by the developers.

#### What does a hardware fault look like?

If you see a message of the following form at the end of standard
output:
```
slurmstepd: error: *** STEP 7871129.0 ON nid001520 CANCELLED AT 2024-10-23T20:04:10 DUE TO NODE FAILURE ***
```
it means a hardware failure on the node has caused the job to crash.
These failures are detected automatically by the system (the hardware
gets restarted or replaced), and the time used will not be charged
against your budget. This is merely "unlucky": please just resubmit
the same job.

#### Job cancelled owing to time limit

Jobs reaching their time limit will be cancelled by the queue system
with a message of the form:
```
slurmstepd: error: *** STEP 7871128.0 ON nid001258 CANCELLED AT 2024-10-24T01:21:34 DUE TO TIME LIMIT ***
```
First, it is a good idea to have an expectation about how long your job
will take. This may prevent surprises. If you don't have an idea, we
will recommend looking at a smaller or shorter problem until you do.
Check the time limit you have specified via the `--time` option
to `sbatch`.

There are a number of possible remedies if a longer time is required:

1. Consider using the `long` QoS option; see the [Quality of Service](../user-guide/scheduler.md/#quality-of-service-qos) descriptions.
2. Exceptionally, consider using a [reservation](../user-guide/scheduler.md/#reservations).

If you are expecting output from your program, but see nothing, you can try
using the `--unbuffered` option to `srun` to make sure output appears
immediately in the standard output (or error), rather than being
buffered by the system (in which case it appears in discrete chunks).

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
