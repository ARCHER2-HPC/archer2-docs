# Data migration from the ARCHER2 4-cabinet system to the ARCHER2 full system

This short guide explains how to move data from from the work file system on the ARCHER2 4-cabinet system
to the ARCHER2 full system. Your space on the home file system is shared between the ARCHER2 4-cabinet system 
and the ARCHER2 full system so everything from your home directory is already effectively
transferred.

!!! note
    This section assumes that you have an active ARCHER2 4-cabinet system 
    and ARCHER2 full system account, and that you have successfully logged 
    in to both accounts.
    
!!! tip
    Unlike normal access, ARCHER2 4-cabinet system to ARCHER2 full system transfer 
    has been set up to require only one form of authentication. You will only need
    one factor to authenticate from the 4-cab to the full system or vice versa. This
    factor can be either an SSH key (that has been registered against your account in 
    SAFE) or you can use your passowrd. If you have a large amount of data to transfer
    you may want to setup a passphrase-less SSH key on ARCHER2 full system and
    [use the data analysis nodes](../user-guide/data.md) to run transfers via a
    Slurm job.

## Transferring data interactively from the 4-cabinet system to the full system

First, login to the ARCHER2 4-cabinet system (making sure to change `auser` 
to your username):

    ssh auser@login-4c.archer2.ac.uk

Then, combine important research data into a single archive file using the 
following command:

    tar -czf all_my_files.tar.gz file1.txt file2.txt directory1/
    
Please be selective -- the more data you want to transfer, the more time it 
will take.

Unpack the archive file in the destination directory

    tar -xzf all_my_files.tar.gz

### Transferring data using `rsync` (recommended)

Begin the data transfer from the ARCHER2 4-cabinet system to the ARCHER2 full 
system using `rsync`:

    rsync -Pv all_my_files.tar.gz a2user@login.archer2.ac.uk:/work/t01/t01/a2user

When running this command, you will be prompted to enter your ARCHER2
password -- this is the same password for the ARCHER2 4-cabinet system 
and the ARCHER2 full system. Enter it and the data transfer will begin. 
Remember to replace `a2user` with your ARCHER2 username, and `t01` with 
the budget associated with that username.

We use the `-P` flag to allow partial transfer -- the same
command could be used to restart the transfer after a loss of
connection. We move our research archive to our project work directory 
on the ARCHER2 full system.

### Transferring data using scp

If you are unconcerned about being able to restart an interrupted
transfer, you could instead use the `scp` command,

    scp all_my_files.tar.gz a2user@login.archer2.ac.uk:/work/t01/t01/a2user/

but `rsync` is recommended for larger transfers.


### Transferring data via the serial queue

It may be convenient to submit long data transfers to the serial queue.
In this case, a number of simple preparatory steps are required to
authenticate:

1. On the full system, create a new ssh key pair without passphrase (just
   press return when prompted).
2. Add the new public key to SAFE against your machine account.
3. Use this key pair for ``ssh/scp`` commands in the serial queue to
   authenticate. As
   it has been arranged that only one of ssh key/password are required
   between the serial nodes and the 4-cabinet system,
   this is sufficient.

An example serial queue script using ``rsync`` might be:

```
#!/bin/bash

# Slurm job options (job-name, job time)

#SBATCH --partition=serial
#SBATCH --qos=serial

#SBATCH --time=02:00:00
#SBATCH --ntasks=1

# Replace [budget code] below with your budget code

#SBATCH --account=[budget code] 

# Issue appropriate rsync command

rsync -av --stats --progress --rsh="ssh -i ${HOME}/.ssh/id_rsa_batch" \
      user-01@login-4c.archer2.ac.uk:/work/proj01/proj01/user-01/src \
      /work/proj01/proj01/user-01/destination
```
where ``${HOME}/.ssh/id_rsa_batch`` is the new ssh key.
Note that the ``${HOME}`` directory is visible from the serial nodes
on the full system, so ssh key pairs in ``${HOME}/.ssh`` are available. 